## 状態別 VM の一覧を取得する  
Azure CLI で状態別（稼働中、割り当て解除済み等）の VM 一覧を取得します。  

作業するサブスクリプションを決めます。  
```
$ az account set -s < SubscriptionID >
```  
簡単に VM 一覧をコマンドラインで見る場合、`az vm list -o table`を良く使うと思います。  

```
$ az vm list -o table
Name                 ResourceGroup               Location       Zones
-------------------  --------------------------  -------------  -------
ake-vit-singer-test  AKE-VIT-SINGER-TEST         southeastasia
ake-test-01          AKE-TEST-01                 japaneast
ake-test-02          AKE-TEST-01                 japaneast
ake-vit-tokyo-test   AKE-VIT-TOKYO-TEST          japaneast
cspjpeast2test       JPEAST_PRODUCTION_TEST      japaneast
intraADbktest        JPEAST_PRODUCTION_TEST      japaneast
intraADtest          JPEAST_PRODUCTION_TEST      japaneast
intraADtest-BK       JPEAST_PRODUCTION_TEST      japaneast
intrardgwtest03      JPEAST_PRODUCTION_TEST      japaneast
intrardgwtest04      JPEAST_PRODUCTION_TEST      japaneast
intrardgwtest05      JPEAST_PRODUCTION_TEST      japaneast
intrardgwtest06      JPEAST_PRODUCTION_TEST      japaneast
KX7-VM7-test         JPEAST_PRODUCTION_TEST      japaneast
KX8-VM8              JPEAST_PRODUCTION_TEST      japaneast
RDS-test             JPEAST_PRODUCTION_TEST      japaneast
RDWA-test            JPEAST_PRODUCTION_TEST      japaneast
nsg-test-sv01        AB30_TEST_RSG               japaneast
nsg-test-sv02        AB30_TEST_RSG               japaneast
ds-attack            AC20-246801                 japaneast
ds-target            AC20-246801                 japaneast
fumidai              KYOUTSUKIBAN                japaneast
jobsche-test-sv2     KYOUTSUKIBAN                japaneast
jobsheduler-mgr      KYOUTSUKIBAN                japaneast
pdns-slave           KYOUTSUKIBAN                japaneast
watch-syslog         KYOUTSUKIBAN                japaneast
backend-win          WAF                         japaneast
paloalto50           WAF                         japaneast
paloalto51           WAF                         japaneast
waf-GW0              WAF                         japaneast
$
```  
`az vm list -o table`だと、VM の状態が判りません。存在が判るだけです。  
各 VM の状態が判るリストを取得します。  

```
$ az vm list -d --query "[].{VMName:name, PrivateIP:privateIps, ResourceGroup:resourceGroup, PowerState:powerState}" -o table
VMName               PrivateIP                                   ResourceGroup               PowerState
-------------------  ------------------------------------------  --------------------------  --------------
ake-vit-singer-test  10.0.2.4                                    AKE-VIT-SINGER-TEST         VM deallocated
ake-test-01          10.0.6.4                                    AKE-TEST-01                 VM deallocated
ake-test-02          10.0.6.5                                    AKE-TEST-01                 VM running
ake-vit-tokyo-test   10.0.1.4                                    AKE-VIT-TOKYO-TEST          VM deallocated
cspjpeast2test       10.0.5.6                                    JPEAST_PRODUCTION_TEST      VM deallocated
intraADbktest        10.0.3.29                                   JPEAST_PRODUCTION_TEST      VM deallocated
intraADtest          10.0.3.27                                   JPEAST_PRODUCTION_TEST      VM deallocated
intraADtest-BK       10.0.3.22                                   JPEAST_PRODUCTION_TEST      VM deallocated
intrardgwtest03      10.0.3.24                                   JPEAST_PRODUCTION_TEST      VM deallocated
intrardgwtest04      10.0.3.25                                   JPEAST_PRODUCTION_TEST      VM deallocated
intrardgwtest05      10.0.5.4                                    JPEAST_PRODUCTION_TEST      VM deallocated
intrardgwtest06      10.0.5.5                                    JPEAST_PRODUCTION_TEST      VM deallocated
KX7-VM7-test         10.0.3.4                                    JPEAST_PRODUCTION_TEST      VM deallocated
KX8-VM8              10.0.5.20                                   JPEAST_PRODUCTION_TEST      VM deallocated
RDS-test             10.0.3.28                                   JPEAST_PRODUCTION_TEST      VM deallocated
RDWA-test            10.0.3.26                                   JPEAST_PRODUCTION_TEST      VM deallocated
nsg-test-sv01        192.168.3.4                                 AB30_TEST_RSG               VM running
nsg-test-sv02        192.168.4.5                                 AB30_TEST_RSG               VM deallocated
ds-attack            10.0.4.5                                    AC20-246801                 VM deallocated
ds-target            10.0.4.6                                    AC20-246801                 VM deallocated
fumidai              10.0.64.4                                   KYOUTSUKIBAN                VM running
jobsche-test-sv2     10.0.64.13                                  KYOUTSUKIBAN                VM deallocated
jobsheduler-mgr      10.0.64.5                                   KYOUTSUKIBAN                VM running
pdns-slave           192.168.4.4                                 KYOUTSUKIBAN                VM deallocated
watch-syslog         10.0.64.9                                   KYOUTSUKIBAN                VM running
backend-win          10.0.88.69                                  WAF                         VM running
paloalto50           10.0.88.5,10.0.88.21,10.0.88.22,10.0.88.37  WAF                         VM deallocated
paloalto51           10.0.88.4,10.0.88.20,10.0.88.36             WAF                         VM deallocated
waf-GW0              10.0.66.4,10.0.66.5                         WAF                         VM running
k_suga@Azure:~$
```  
このように、稼働中、割り当て解除済み等の状態が判る一覧になっています。  

稼働中の VM 一覧を取得してみます。  

```
$ az vm list -d --query "[?powerState=='VM running'].{VMName:name, PrivateIP:privateIps, ResourceGroup:resourceGroup, PowerState:powerState}" -o table
VMName           PrivateIP            ResourceGroup    PowerState
---------------  -------------------  ---------------  ------------
ake-test-02      10.0.6.5             AKE-TEST-01      VM running
nsg-test-sv01    192.168.3.4          AB30_TEST_RSG    VM running
fumidai          10.0.64.4            KYOUTSUKIBAN     VM running
jobsheduler-mgr  10.0.64.5            KYOUTSUKIBAN     VM running
watch-syslog     10.0.64.9            KYOUTSUKIBAN     VM running
backend-win      10.0.88.69           WAF              VM running
waf-GW0          10.0.66.4,10.0.66.5  WAF              VM running
$
```  
稼働中の VM 一覧が取得できました。  

次に割り当て解除済みの VM 一覧を取得してみます。  
```
$ az vm list -d --query "[?powerState=='VM deallocated'].{VMName:name, PrivateIP:privateIps, ResourceGroup:resourceGroup, PowerState:powerState}" -o table
Unable to load extension 'resource-graph'. Use --debug for more information.
VMName               PrivateIP                                   ResourceGroup               PowerState
-------------------  ------------------------------------------  --------------------------  --------------
ake-vit-singer-test  10.0.2.4                                    AKE-VIT-SINGER-TEST         VM deallocated
ake-test-01          10.0.6.4                                    AKE-TEST-01                 VM deallocated
ake-vit-tokyo-test   10.0.1.4                                    AKE-VIT-TOKYO-TEST          VM deallocated
cspjpeast2test       10.0.5.6                                    JPEAST_PRODUCTION_TEST      VM deallocated
intraADbktest        10.0.3.29                                   JPEAST_PRODUCTION_TEST      VM deallocated
intraADtest          10.0.3.27                                   JPEAST_PRODUCTION_TEST      VM deallocated
intraADtest-BK       10.0.3.22                                   JPEAST_PRODUCTION_TEST      VM deallocated
intrardgwtest03      10.0.3.24                                   JPEAST_PRODUCTION_TEST      VM deallocated
intrardgwtest04      10.0.3.25                                   JPEAST_PRODUCTION_TEST      VM deallocated
intrardgwtest05      10.0.5.4                                    JPEAST_PRODUCTION_TEST      VM deallocated
intrardgwtest06      10.0.5.5                                    JPEAST_PRODUCTION_TEST      VM deallocated
KX7-VM7-test         10.0.3.4                                    JPEAST_PRODUCTION_TEST      VM deallocated
KX8-VM8              10.0.5.20                                   JPEAST_PRODUCTION_TEST      VM deallocated
RDS-test             10.0.3.28                                   JPEAST_PRODUCTION_TEST      VM deallocated
RDWA-test            10.0.3.26                                   JPEAST_PRODUCTION_TEST      VM deallocated
nsg-test-sv02        192.168.4.5                                 AB30_TEST_RSG               VM deallocated
ds-attack            10.0.4.5                                    AC20-246801                 VM deallocated
ds-target            10.0.4.6                                    AC20-246801                 VM deallocated
jobsche-test-sv2     10.0.64.13                                  KYOUTSUKIBAN                VM deallocated
pdns-slave           192.168.4.4                                 KYOUTSUKIBAN                VM deallocated
paloalto50           10.0.88.5,10.0.88.21,10.0.88.22,10.0.88.37  WAF                         VM deallocated
paloalto51           10.0.88.4,10.0.88.20,10.0.88.36             WAF                         VM deallocated
k_suga@Azure:~$
```  

ARM ポータルでは一台ずつ個別に確認しなければならない VM の状態を CLI では一覧で確認することができます。  
