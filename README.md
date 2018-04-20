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

* The custom SFLoginViewController then triggers the following call to SFUserAccountManager when the login button is pressed.

```
 __weak typeof (self) weakSelf = self;
    [[SFUserAccountManager sharedInstance] loginWithUserName:self.userNameTextField.text password:self.passwordTextField.text  completionBlock:^(SFOAuthInfo * authInfo, SFUserAccount * userAccount) {
         [SFUserAccountManager sharedInstance].currentUser = userAccount;
       
       
    } failure:^(SFOAuthInfo * authInfo, NSError * errror) {
         __weak typeof (weakSelf) strongSelf = weakSelf;
      
    } viewController: self];
    
```

* Find the above example NativeLoginViewController.m & NativeLoginViewController.xib in the SmartSyncExplorer sample. 

* Run the sample. 

###  Mobile SDK Internals

* The custom SFLoginViewController that is configured in the app delegate is presented to the user, whenever necessary. A  webview  is required (can be invisible). 


* The Mobile SDK then submits  a POST method call  to the endpoint for embedded url   (<loginhost>/servlet/servlet.loginwidgetcontroller) with the following url encoded post body.

     * type=login
     * username
     * password
     * startURL (urlencodedstarturl)
       * startURL="services/oauth2/authorize?client_id=<client_id>&redirect_uri=<redirect_uri>&response_type=token"

* The embbedded enpoint returns a JSON with a result that contains a url that is then loaded into the webview.
    ```
    {
      result = "https://na59.salesforce.com/secur/frontdoor.jsp?sid=00Df400000256Qi%21AR4AQCkKDDSYtwqLb5lQJTxLDB5v1pPS6jEwpHrGFWb0PX.fzjRNuNVEW59JEBfBT5qv73q3RoQMgR3SUozNGvjj9rbKgGVK&retURL=https%3A%2F%2Fna59.salesforce.com%2Fservlet%2Fnetworks%2Fsession%2Fcreate%3Furl%3D%252Fdemo%252Fservices%252Foauth2%252Fauthorize%253Fclient_id%253D3MVG9zlTNB8o8BA0hXyea5QTe0bQwSvcHdxbxgB9WtIalxYfhq.qX4E5wbNUoSCQQbqxMg6W0sySQLECB9JUn%2526redirect_uri%253Dtrooper%253A%252F%252Foauth%252Fdone%2526response_type%253Dtoken%26site%3D0DMf4000000gwV7%26inst%3Df4&apv=1&allp=1&untethered=&cshc=4000000TY1z400000256Qi&refURL=https%3A%2F%2Fna59.salesforce.com%2Fsecur%2Ffrontdoor.jsp";
  }

    ```
* Once the webview is loaded with the result url, the mobile sdk should complete the auth flow eventually invoking the completion blocks specified with the SFUserAccountManager:loginWithUserName:pasword method.




  
