# SimpleAuth.Mvc

Simple Auth demo showcasing integration with ServiceStack and ASP.NET .NET Core MVC

[![](https://raw.githubusercontent.com/ServiceStack/Assets/master/img/livedemos/mvc.png)](http://mvc.netcore.io)

### Deploy to OpenShift

First, create your OAuth application for Twitter, Facebook, GitHub which you'd like to use for OAuth. Then create OpenShift secret with these parameters. A below sample is working for OpenShift 3.3+, if you use OpenShift 3.2- please base64 encoded the whole value included line break and specifiy the encoded value with "data" field like below.

```
$ cat simpleauthmvc.yaml 
apiVersion: "v1"
kind: "Secret"
metadata:
  name: "simpleauthmvc"
stringData: 
  authprovidersetting: |
    DebugMode True
    oauth.RedirectUrl http://<your_app_domain>/
    oauth.CallbackUrl http://<your_app_domain>/auth/{0}
    oauth.twitter.ConsumerKey <Twitter_ConsumerKey>
    oauth.twitter.ConsumerSecret <Twitter_ConsumerSecretKey> 
    oauth.facebook.Permissions email
    oauth.facebook.AppId <Facebook_AppID>
    oauth.facebook.AppSecret <Facebook_AppSecret>
    oauth.github.Scopes user
    oauth.github.ClientId <Gitub_ClientId>
    oauth.github.ClientSecret <GitHub_ClientSecret>
$ oc create -f simpleauthmvc.yaml 
```

```
apiVersion: "v1"
kind: "Secret"
metadata:
  name: "simpleauthmvc"
data: 
  authprovidersetting: <base64_encoded_value>
```

Next create an app.

```
$ oc new-app --template=s2i-aspnet -p APPLICATION_NAME=simpleauthmvc,GIT_URI=https://github.com/tanaka-takayoshi/SimpleAuth.Mvc.git,GIT_CONTEXT_DIR=src/Mvc,GIT_REF=openshift -l app=simpleauthmvc
```

Finally edit deployment config to attach secret volume.

```
$ oc edit dc simpleauthmvc

spec:
<snip>
  containers:
    - name: simpleauthmvc
      <snip>
      volumeMounts:
          # name must match the volume name below
          - name: secret-volume
            mountPath: /etc/secret-volume
            readOnly: true
  <snip>
  volumes:
    - name: secret-volume
      secret:
        secretName: simpleauthmvc
  restartPolicy: Never
```

#### Features

  - [ServiceStack Integration with MVC](http://docs.servicestack.net/servicestack-integration.html)
  - [Authentication and Authorization](http://docs.servicestack.net/authentication-and-authorization.html)
    - Twitter
    - Facebook
    - GitHub
    - VK
    - Yandex
  - [JS Utils and Forms AutoBinding](http://docs.servicestack.net/ss-utils-js.html)
