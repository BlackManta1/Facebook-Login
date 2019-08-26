# Facebook-Login
Learn how to implement a Facebook Login and retrieve a different kind of data.

<p align="left">
<img src="https://user-images.githubusercontent.com/46055179/62839673-1bdf8980-bc9e-11e9-9997-9812e217f999.jpeg" width="250">
<img src="https://user-images.githubusercontent.com/46055179/59964252-87fc0780-950f-11e9-9e0a-aeee12033857.PNG" width="250">
</p>

<p align="left">
<img src="https://user-images.githubusercontent.com/46055179/59964251-87637100-950f-11e9-8005-a33e2338b0c7.PNG" width="250">
<img src="https://user-images.githubusercontent.com/46055179/59964250-87637100-950f-11e9-981b-33d9ad52cdf7.PNG" width="250">
</p>

## Key informations:
[developers.facebook.com](https://developers.facebook.com/docs/graph-api/reference/user)

## Most useful code:

```swift

import UIKit
import FBSDKCoreKit
import FBSDKLoginKit

class ViewController: UIViewController, LoginButtonDelegate {
    
    override func viewDidLoad() {
        super.viewDidLoad()
        // Do any additional setup after loading the view.
        
        let loginButton = FBLoginButton()
        
        // https://developers.facebook.com/docs/facebook-login/permissions/
        //loginButton.permissions = ["public_profile", "email", "user_birthday", "user_gender", "user_hometown"]
        loginButton.permissions = ["public_profile", "email"]
        // certaines informations sont soumis a une review de la part de Facebook
        
        loginButton.delegate = self
        //loginButton.center = view.center
        loginButton.frame = CGRect(x: 16, y: view.frame.maxY/2, width: view.frame.width - 32, height: 50)
        view.addSubview(loginButton)
        
        if AccessToken.isCurrentAccessTokenActive {
            // User is logged in, do work such as go to next view controller.
            print("User is already log in")
        }
        
    }
    
    func loginButton(_ loginButton: FBLoginButton, didCompleteWith result: LoginManagerLoginResult?, error: Error?) {
        
        if let err = error {
            print(err.localizedDescription)
        } else {
            if let myResult = result {
                
                if myResult.isCancelled {
                    print("User has canceled login !")
                } else if let contains = result?.grantedPermissions.contains("email"), contains == true {
                    
                    // je suis choque c'est incroyable le nombre d'information que l'on puisse recuperer
                    // religion, politique, situation amoureuse, ta liste d'amis, tes likes, tes photos, etc ...
                    // more details here : https://developers.facebook.com/docs/graph-api/reference/user
                    //let graphRequest = GraphRequest(graphPath: "me", parameters: ["fields" : "id,name,email,birthday,gender,hometown"])
                    let graphRequest = GraphRequest(graphPath: "me", parameters: ["fields" : "id,name,email"])
                    graphRequest.start { (connection, result, error) in
                        if let err = error {
                            print(err.localizedDescription)
                        } else {
                            if let userInformations = result {
                                print(userInformations)
                                
                                // we get now the differents value
                                let infoDictionary = userInformations as? [String: Any]
                                if let name = infoDictionary?["name"] as? String, let email = infoDictionary?["email"] as? String {
                                    
                                    let mayY = loginButton.frame.maxY
                                    let userDetailsLabel = UILabel(frame: CGRect(x: 0, y: mayY, width: self.view.frame.width, height: 200))
                                    userDetailsLabel.numberOfLines = 0
                                    userDetailsLabel.text = "Name : \(name) - Email : \(email)"
                                    userDetailsLabel.textAlignment = .center
                                    self.view.addSubview(userDetailsLabel)
                                }
                            }
                        }
                    }
                }
            }
        }
        
    }
    
    func loginButtonDidLogOut(_ loginButton: FBLoginButton) {
        print("User logged out")
    }
    
}

```
