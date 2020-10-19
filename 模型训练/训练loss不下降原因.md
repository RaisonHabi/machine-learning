现在都有ap来衡量网络收敛程度，loss降低，但是ap不一定增长
## 1.train loss与test loss结果分析
```
train loss 不断下降，test loss不断下降，说明网络仍在学习;

train loss 不断下降，test loss趋于不变，说明网络过拟合;

train loss 趋于不变，test loss不断下降，说明数据集100%有问题;

train loss 趋于不变，test loss趋于不变，说明学习遇到瓶颈，需要减小学习率或批量数目;

train loss 不断上升，test loss不断上升，说明网络结构设计不当，训练超参数设置不当，数据集经过清洗等问题。
```

&nbsp;
## 2.
有1.梯度检验2.训练前检查，3.训练中监控4.首层可视化5.模型融合和优化等等等. 
[Loss和神经网络训练](https://blog.csdn.net/u011534057/article/details/51452564)

loss一直不下降的原因有很多，可以从头到尾滤一遍：
```
1）数据的输入是否正常，data和label是否一致。

2）网络架构的选择，一般是越深越好，也分数据集。 并且用不用在大数据集上pre-train的参数也很重要的

3）loss 对不对。
```

&nbsp;
## 3.性能评价指标
一般来说对于应用深度学习方法的网络模型来说，我们希望网络模型速度快，内存小，精度高，即快、小、好。  
### 3.1map（平均准确度均值）
mAP定义及相关概念
```
mAP: mean Average Precision, 即各类别AP的平均值
AP: PR曲线下面积，后文会详细讲解
PR曲线: Precision-Recall曲线
Precision: TP / (TP + FP)
Recall: TP / (TP + FN)
TP: IoU>0.5的检测框数量（同一Ground Truth只计算一次）
FP: IoU<=0.5的检测框，或者是检测到同一个GT的多余检测框的数量
FN: 没有检测到的GT的数量
```
注意：（1）一般来说mAP针对整个数据集而言的；AP针对数据集中某一个类别而言的；而percision和recall针对单张图片某一类别的。
### 3.2 mAP的具体计算
#### 不同数据集map计算方法.   
由于map是数据集中所有类别AP值得平均，所以我们要计算map，首先得知道某一类别的AP值怎么求。不同数据集的某类别的AP计算方法大同小异，主要分为三种： 
...

#### 计算某一类别的AP.  
由上面概念我们知道，我们计算某一类别AP需要需要绘画出这一类别的PR曲线，所以我们要计算数据集中每张图片中这一类别的percision和reacll。由公式：

Precision: TP / (TP + FP)
Recall: TP / (TP + FN)
只需要统计出TP，FP，FN个数就行了。下面放下代码再作解释（代码原址）：
```
# first load gt
if not os.path.isdir(cachedir):
  os.mkdir(cachedir)
cachefile = os.path.join(cachedir, '%s_annots.pkl' % imagesetfile)
# read list of images
with open(imagesetfile, 'r') as f:
  lines = f.readlines()
imagenames = [x.strip() for x in lines]

if not os.path.isfile(cachefile):
  # load annotations
  recs = {}
  for i, imagename in enumerate(imagenames):
    recs[imagename] = parse_rec(annopath.format(imagename))
    if i % 100 == 0:
      print('Reading annotation for {:d}/{:d}'.format(
        i + 1, len(imagenames)))
  # save
  print('Saving cached annotations to {:s}'.format(cachefile))
  with open(cachefile, 'wb') as f:
    pickle.dump(recs, f)
else:
  # load
  with open(cachefile, 'rb') as f:
    try:
      recs = pickle.load(f)
    except:
      recs = pickle.load(f, encoding='bytes')

# extract gt objects for this class
class_recs = {}
npos = 0
for imagename in imagenames:
  R = [obj for obj in recs[imagename] if obj['name'] == classname]
  bbox = np.array([x['bbox'] for x in R])
  difficult = np.array([x['difficult'] for x in R]).astype(np.bool)
  det = [False] * len(R)
  npos = npos + sum(~difficult)
  class_recs[imagename] = {'bbox': bbox,
                           'difficult': difficult,
                           'det': det}

# read dets
detfile = detpath.format(classname)
with open(detfile, 'r') as f:
  lines = f.readlines()

splitlines = [x.strip().split(' ') for x in lines]
image_ids = [x[0] for x in splitlines]
confidence = np.array([float(x[1]) for x in splitlines])
BB = np.array([[float(z) for z in x[2:]] for x in splitlines])

nd = len(image_ids)
tp = np.zeros(nd)
fp = np.zeros(nd)

if BB.shape[0] > 0:
  # sort by confidence
  sorted_ind = np.argsort(-confidence)
  sorted_scores = np.sort(-confidence)
  BB = BB[sorted_ind, :]
  image_ids = [image_ids[x] for x in sorted_ind]

  # go down dets and mark TPs and FPs
  for d in range(nd):
    R = class_recs[image_ids[d]]
    bb = BB[d, :].astype(float)
    ovmax = -np.inf
    BBGT = R['bbox'].astype(float)

    if BBGT.size > 0:
      # compute overlaps
      # intersection
      ixmin = np.maximum(BBGT[:, 0], bb[0])
      iymin = np.maximum(BBGT[:, 1], bb[1])
      ixmax = np.minimum(BBGT[:, 2], bb[2])
      iymax = np.minimum(BBGT[:, 3], bb[3])
      iw = np.maximum(ixmax - ixmin + 1., 0.)
      ih = np.maximum(iymax - iymin + 1., 0.)
      inters = iw * ih

      # union
      uni = ((bb[2] - bb[0] + 1.) * (bb[3] - bb[1] + 1.) +
             (BBGT[:, 2] - BBGT[:, 0] + 1.) *
             (BBGT[:, 3] - BBGT[:, 1] + 1.) - inters)

      overlaps = inters / uni
      ovmax = np.max(overlaps)
      jmax = np.argmax(overlaps)

    if ovmax > ovthresh:
      if not R['difficult'][jmax]:
        if not R['det'][jmax]:
          tp[d] = 1.
          R['det'][jmax] = 1
        else:
          fp[d] = 1.
    else:
      fp[d] = 1.

# compute precision recall
fp = np.cumsum(fp)
tp = np.cumsum(tp)
rec = tp / float(npos)
# avoid divide by zero in case the first detection matches a difficult
# ground truth
prec = tp / np.maximum(tp + fp, np.finfo(np.float64).eps)
ap = voc_ap(rec, prec, use_07_metric)

return rec, prec, ap
```
 

&nbsp;
## reference
[训练loss不下降原因集合](https://blog.csdn.net/jacke121/article/details/79874555)   
[目标检测的性能评价指标](https://zhuanlan.zhihu.com/p/70306015)
