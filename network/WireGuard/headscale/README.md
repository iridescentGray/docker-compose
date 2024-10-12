# docker启动headscale+headscale_ui
 参考 https://headscale.net/setup/install/container/

## 启动命令
    # 添加如下内容到host文件
    127.0.0.1 headscale.tmplinux.com
    # 执行
    docker compose up -d


##  启动后的地址
    headscale address http://headscale.tmplinux.com
    headscale-ui address http://headscale.tmplinux.com/web

## headscale-ui添加headscale 的api key
    # 创建apikey
    docker exec -it headscale headscale apikey create
    # 把api key填到如下页面 完成 headscale-ui和headscale之间的打通
    http://headscale.tmplinux.com/web/settings.html

## tailscale客户端加入headscale控制节点
    # 创建用户
    docker exec -it headscale headscale users create test
、  # 注册客户端机器方法1: 手动注册
    tailscale up --login-server http://headscale.tmplinux.com        //获取机器key
    docker exec -it headscale headscale nodes register --user test --key <机器key>  //使用上一步生成的key

    # 注册客户端机器方法2:使用预先验证的密钥注册机器(简单)
    docker exec -it headscale headscale preauthkeys create --user test --reusable --expiration 24h    //创建密钥
    tailscale up --login-server http://headscale.tmplinux.com --authkey <密钥>                        //tailscale客户端 用密钥注册机器
