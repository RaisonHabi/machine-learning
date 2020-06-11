## 参考代码
```
def plot_roc_ks_curve(true, pred):  
    fpr, tpr, thresholds= metrics.roc_curve(true, pred) #信用评分里面1是好人（白样本）
    plt.figure()
    plt.plot(fpr, tpr, color ='b', label='auc={:.4f}'.format(metrics.roc_auc_score(true, pred)))
    plt.plot(fpr, fpr, color = 'g')
    plt.legend()
    plt.title('ROC Curve', fontsize=15)
    plt.show()
    
    mat = pd.DataFrame({'thresholds':thresholds,'TPR':tpr,'FPR':fpr})
    ks_value = max(abs(fpr-tpr))
    x_0 = []
    for i in range(0,len(fpr)):
        x_0.append(i/len(fpr))
    plt.figure()
    plt.plot(x_0, fpr, label='cum_good_rate')
    plt.plot(x_0, tpr, label='cum_bad_rate') #cum_label=1_rate这里label=1为好人，但是还是叫bad_rate
    plt.plot(x_0, abs(fpr-tpr), label='diff')
#     # 标记ks
    x = np.argwhere(abs(fpr-tpr) == ks_value)[0, 0]/len(fpr)
    print(x, ks_value)
    plt.plot((x, x), (0, ks_value), label='x = {:.2f}, ks - {:.3f}'.format(x,ks_value), color='r', marker='o', markerfacecolor='r', markersize=5)
    plt.scatter((x, x), (0, ks_value), color='r')
    plt.legend()
    plt.title('KS Curve', fontsize=15)
    plt.show()
```

示例：
```
plot_roc_ks_curve(train_y['base__dpd30'], train_lgb)
plot_roc_ks_curve(valid_y['base__dpd30'], valid_lgb)
```
