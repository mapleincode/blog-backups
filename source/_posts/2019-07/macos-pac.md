---
title: MAC OS 通过脚本配置自动代理 & 关闭自动代理
date: 2018-05-29 11:31:29
tags: [ "mac os" ]
---

因为在家里搞了一个 PAC 文件用来科学上网。但是上班用的 MAC 因为要带到公司，所以在公司用的是 Sha*socks。因为启用  SS 会覆盖原有的配置，而如果已经有了 PAC 配置，在不消除设置的情况下，在外网同样也是无法上网的。



所以找了一个网上的教程，搞到了两个配置:



## 设置自动代理

`setpac.sh`

```sh
#!/bin/sh
####################################################################################################
#
# More information: https://macmule.com/2014/12/07/how-to-change-the-automatic-proxy-configuration-url-in-system-preferences-via-a-script/
#
# GitRepo: https://github.com/macmule/setAutomaticProxyConfigurationURL
#
# License: http://macmule.com/license/
#
####################################################################################################

# HARDCODED VALUES ARE SET HERE
autoProxyURL="PAC_ADDRESS"
# CHECK TO SEE IF A VALUE WAS PASSED FOR $4, AND IF SO, ASSIGN IT
if [ "$4" != "" ] && [ "$autoProxyURL" == "" ]; then
    autoProxyURL=$4
fi
# Detects all network hardware & creates services for all installed network hardware
/usr/sbin/networksetup -detectnewhardware
IFS=$'\n'
    #Loops through the list of network services
    for i in $(networksetup -listallnetworkservices | tail +2 );
    do
        # Get a list of all services beginning 'Ether' 'Air' or 'VPN' or 'Wi-Fi'
        # If your service names are different to the below, you'll need to change the criteria
        if [[ "$i" =~ 'Ether' ]] || [[ "$i" =~ 'Air' ]] || [[ "$i" =~ 'VPN' ]] || [[ "$i" =~ 'Wi-Fi' ]] ; then
            autoProxyURLLocal=`/usr/sbin/networksetup -getautoproxyurl "$i" | head -1 | cut -c 6-`
            # Echo's the name of any matching services & the autoproxyURL's set
            echo "$i Proxy set to $autoProxyURLLocal"
            # If the value returned of $autoProxyURLLocal does not match the value of $autoProxyURL for the interface $i, change it.
            if [[ $autoProxyURLLocal != $autoProxyURL ]]; then
                /usr/sbin/networksetup -setautoproxyurl $i $autoProxyURL
                echo "Set auto proxy for $i to $autoProxyURL"
            fi
        fi
        # Enable auto proxy once set
        /usr/sbin/networksetup -setautoproxystate "$i" on
        echo "Turned on auto proxy for $i"
    done
unset IFS
# Echo that we're done
echo "Auto proxy present, correct & enabled for all targeted interfaces"

```

## 删除自动代理

`delpac.sh`

```sh
#!/bin/sh
####################################################################################################
#
# More information: https://macmule.com/2014/12/07/how-to-change-the-automatic-proxy-configuration-url-in-system-preferences-via-a-script/
#
# GitRepo: https://github.com/macmule/setAutomaticProxyConfigurationURL
#
# License: http://macmule.com/license/
#
####################################################################################################

# HARDCODED VALUES ARE SET HERE
autoProxyURL="http://192.168.2.110:6888/pac"
# CHECK TO SEE IF A VALUE WAS PASSED FOR $4, AND IF SO, ASSIGN IT
if [ "$4" != "" ] && [ "$autoProxyURL" == "" ]; then
    autoProxyURL=$4
fi
# Detects all network hardware & creates services for all installed network hardware
/usr/sbin/networksetup -detectnewhardware
IFS=$'\n'
    #Loops through the list of network services
    for i in $(networksetup -listallnetworkservices | tail +2 );
    do
        # Enable auto proxy once set
        /usr/sbin/networksetup -setautoproxystate "$i" off
        echo "Turned off auto proxy for $i"
    done
unset IFS
# Echo that we're done
echo "Auto proxy has be disabled for all targeted interfaces"

```

然后在 `.zshrc`中配置了:

```
alias setpac="sh setpac.sh"
alias delpac="sh delpac.sh"
```

> `setpac.sh` 和 `delpac.sh` 需要添加执行权限。`sudo chmod +x *.sh`
