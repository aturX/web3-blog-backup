## 如何快速创建1000个以太坊钱包账户？

## 内容简介

本篇文章完成内容如下：

-  通过Python 脚本创建1000个以太坊的钱包地址
-  创建相应的私钥和公钥
-  数据保存至CSV文件中

创建大量的账号可以用于批量的完成一些任务。





脚本使用到的库是： 


![image.png](https://cdn.hashnode.com/res/hashnode/image/upload/v1643273674752/5CufcPRsz.png)



## 1. 导入依赖 


![image.png](https://cdn.hashnode.com/res/hashnode/image/upload/v1643273913403/DNyqnLaty.png)

## 2. 创建钱包 

![image.png](https://cdn.hashnode.com/res/hashnode/image/upload/v1643273941114/TS3Y3lwfp.png)

## 3. 保存数据 

![image.png](https://cdn.hashnode.com/res/hashnode/image/upload/v1643273968373/l7AkyATjQi.png)

## 4. 运行脚本 

![image.png](https://cdn.hashnode.com/res/hashnode/image/upload/v1643273994040/sGVUDm-fp.png)

## 5. 完整代码

```
from eth_account import Account
from web3 import Web3
import csv 

# 安装： pip3 install eth_account
#       pip3 install web3

def createNewETHWallet():
    wallets = []

    for id in range(1000):
        # 添加一些随机性
        account = Account.create('Random  Seed'+str(id))
        
        # 私钥
        privateKey = account._key_obj

        # 公钥
        publicKey = privateKey.public_key

        # 地址
        address = publicKey.to_checksum_address()

        wallet = {
            "id": id,
            "address": address,
            "privateKey": privateKey,
            "publicKey": publicKey
        }
        wallets.append(wallet.values())
    
    return wallets


def saveETHWallet(jsonData):
    with open('wallets.csv', 'w', newline='') as csv_file:
        csv_writer = csv.writer(csv_file)
        csv_writer.writerow(["序号", "钱包地址", "私钥", "公钥"])
        csv_writer.writerows(jsonData)


if __name__ == "__main__":

    print("---- 开始创建钱包 ----")
    # 创建 1000 个随机钱包
    wallets = createNewETHWallet()

    # 保存至 csv 文件
    saveETHWallet(wallets)
    print("---- 完成 ----")

```


## 最终数据：


![image.png](https://cdn.hashnode.com/res/hashnode/image/upload/v1643274041293/oRqGnaVS1.png)


