### Prerequisites
1. Create a Salesforce Community
2. Create a Connected App. For this demo to work, Intermediate authorization screens or 2FA has to be disabled. You can do so by changing the settings for 2FA under "session settings". 
3. In Manage Connected Apps set the option "Admin Approved Users are pre-authorized" in oauth policies. 
4. Assign connected app to the user's profile to suppress the auth screen. 

### Follow steps to run demo

* Checkout the Mobile SDK source from 
```

git clone https://github.com/trooper2013/SalesforceMobileSDK-iOS
git checkout native_login_spike

cd SalesforceMobileSDK-iOS
./install.sh
open SalesforceMobileSDK.xcworkspace
```

* Set the loginHost to point to communities in the AppDelegate of SmartSyncExplorer

```
[SFUserAccountManager sharedInstance].loginHost = @"trooper-developer-edition.na59.force.com/demo";


```
* Set the native login view to a viewcontroller that extends SFLoginViewController. e.g.

```
 [SFUserAccountManager sharedInstance].loginViewControllerConfig.loginViewControllerCreationBlock = ^SFLoginViewController * {
            NativeLoginViewController *controller = [[NativeLoginViewController alloc] initWithNibName:nil bundle:nil];
            return controller;
  };
```

* Find the example NativeLoginViewController.m & NativeLoginViewController.xib in the SmartSyncExplorer sample. 

* Run the sample. 




  
