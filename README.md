## 主仓库地址: https://github.com/zero205/JD_tencent_scf/tree/main
## 为了您的数据安全,必须将仓库私有,具体内容请到主仓库地址查看.
## 当前:配置文件部署方式(暂时只推荐有一点点基础的用户使用)
## 配置文件部署方式说明:
1. 需要配合配置文件分支config使用.(请运行对应action获取分支)
2. TENCENT开头的NAME/REGION(SCF_REGION)/MEMORYSIZE/TIMEOUT依然使用Secrets
3. PAT依然使用Secrests
4. TENCENT的SECRET_ID/SECRET_KEY请填入config分支的.env文件
5. 其余所有环境变量填入config分支的config.yml格式请照第一行填写(追加在后面,每个一行,注意冒号后方空格).
6. 金融签到可以在config分支新建JRBODY.txt文件,按jd_bean_sign开头格式写入JRBODY.(此条未来会逐步废弃,请按下一条将JRBODY.txt放入diy文件夹)
### 面向大佬/高级用户:
1. config分支内diy文件夹内所有内容(如果有的话)会覆盖/加入仓库文件并部署上去(当然包含serverless.yml).
2. config分支diy.sh如果有的话会自动运行(仅面向高级用户)
3. 添加一个Secret:EXPERIMENT,值为任意.即可使用实验性特性(执行顺序在上述两个diy操作之前,所以diy内容如果跟实验性特性有冲突,会覆盖,也方便各位大佬施展自己的骚操作).具体请见下方.
### 即将发生的更新(实验性特性:功能更强,费用更低.仅限高级用户. 尚未更新!普通用户先别管这段):
https://github.com/Ca11back/scf-experiment

欢迎各位大佬尝试/PR实验性特性. 嗯,开发时候没发现,结果实验性特性收费更低了...具体内容请看群内置顶/或回复'云函数扣费'关键字,欢迎各位大佬探讨.

项目内也包含一些简易(也许还实用)diy.sh的example,需要可以看一下.

**前排提醒,diy文件夹下confg_diy.json中的内容会对config.json也就是官方默认配置的规则是:有则覆盖,无则合并.比如config.json中某脚本4小时运行一次,我在confg_diy.json中3小时运行一次,则规则覆盖.如果那个脚本官方配置中没有,则使用confg_diy.json的配置**
1. 在diy文件夹下新加入config_diy.json来自定义脚本运行,格式与config.json相同. 比如我想自定义运行jd_cfd_loop,从每天6点到23点.则config_diy为:
```json
{
    "jd_cfd_loop": [6, 7, 8, 9, 10, 11, 12, 13, 14, 15, 16, 17, 18, 19, 20, 21, 22, 23],
}
```
2. 自定义每个脚本的超时时常为15分钟(只有同步执行timeout参数才有效).注意:TENCENT_TIMEOUT为全局超时,也就是每次整个函数运行时候的超时时间,建议使用默认值3600.(此处例子我想要jd_cfd_loop每3小时运行,注意3两侧没有方括号).
```json
{
    "jd_cfd_loop": 3,
    "params":
    {
        "global":
        {
            "timeout": 15
        }
    }
}
```
3. 自定义某个脚本(例子中是jd_speed_sign)超时时常为50分钟,在执行这个脚本时,超时会使用50分钟,而不是15分钟:
```json
{
    "jd_cfd_loop": 3,
    "params":
    {
        "global":
        {
            "timeout": 15
        },
        "jd_speed_sign":
        {
            "timeout": 50
        }
    }
}
```
4. 启用异步运行: 异步运行脚本运行时间更短(可以考虑用来解决脚本跑不完,但最好的方式还是挪脚本执行时间), 有潜在的冲突问题(京喜类尤其明显),timeout参数不生效,脚本如果异常就只能靠全局超时TENCENT_TIMEOUT来结束.
```json
{
    "jd_cfd_loop": 3,
    "params":
    {
        "global":
        {
            "timeout": 15,
            "exec": "async"
        },
        "jd_speed_sign":
        {
            "timeout": 60
        }
    }
}
```
5. 部分兑换类脚本单独使用一个触发器,且不受timeout参数控制.详见:serverless.yml

## 云函数部分常用变量说明(默认值见仓库serverless.yml):
1. TENCENT_NAME(Secret): 函数名字,修改后注意删除原来名字的函数,名字强烈建议自己搞一个,别用默认的.
2. TENCENT_NAME_MEMORYSIZE(Secret): 运行内存64,128,256.大内存会加快配额消耗.单位Mb
3. TENCENT_TIMEOUT(Secret): 云函数超时时间,单位秒
4. SCF_REGION(Secret): 云函数部署地区,注意修改后删除原地区旧函数
5. NOT_RUN(环境变量): 不运行的脚本.如: jd_cfd&jd_joy&jd_jdfactory

## FAQ(常见问题):
### Github部署日志:ETIMEOUT ERROR
1. 请尝试手动删除/添加config分支.env文件中的GLOBAL_ACCELERATOR_NA=true,尝试关闭/或开启境外加速.(默认开启,且建议开启)
2. 启用随机分钟部署(默认开启,在定时的小时随机抽取分钟进行执行).关闭请直接禁用Random Cron workflow
### Github部署日志:未找到函数执行入口文件
云函数面板删除函数,更改Secret函数名字TENCENT_NAME,重新运行action部署.
### 云函数流量异常高,触发日志与流量日志对不上
解决方法同上

## 其他通知(有时会有临时性通知):

**云函数问题可以入群寻求帮助(仔细阅读教程,需要有一定基础,进群后看置顶反馈流程后再提问).入群地址见主仓库.**

**群内回复'云函数扣费'可以获得云函数扣费分析方法和正常扣费水平.**
