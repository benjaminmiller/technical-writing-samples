### Application registration

Applications and programs wishing to use the Mendeley API must first register to ensure API requests are able to be properly identified. Application registration provides you with OAuth credentials required for user authorization. 

You can register applications, for free, in just a few minutes and you can register as many applications as you wish.
To register an application using the *My Applications* page in the *Developer Portal*:

1. Open [dev.mendeley.com/yourapps.html](http://dev.mendeley.com/yourapps.html) in your web browser and sign in with your Mendeley account's email address and password.
* The applications page contains two panels: a list of existing applications and a five stage form to register a new application. You will use the form to register your application.
* Enter the name of your application in the *application name* text field. The name you use should be concise and easily identify your application to your users. The application name will presented to the user in an authorization panel. Any Unicode (Basic Multilingual Plane only) character can be used in the application name. The application name can be changed later, after registering the application.
* Enter a brief description of your application in the *Description* text area. The description is for your own future reference and will help identify the application. The description can be edited after registering the app.
* Enter the full URL of your OAuth redirect page in the *Redirect URL* text field. The redirect page is displayed to the user immediately after authorization and can be used to determine success or failure of the authorization process. The redirect URL can be edited after registering the app. For more information about OAuth redirection, see [Authorization](authorization_overview.html).
* Press the Generate Secret button to populate the Secret text field with a generated random secret. Alternatively enter an existing secret of your own. **Important**: *make a note of the secret now as you will not be able to retrieve it later*.
* Press the *Submit* button to register the application with the information you entered. On successful registration, your application will be listed in the *My applications* panel.
* Make a note of the ID assigned to your newly created application. The ID is displayed in the first column of the applications list. This numerical value will be used later when requesting authorization through the OAuth service. 

The My Applications page can be used to manage your applications in the future. You can edit the name, redirect URL and description of existing applications. The secret value for existing applications can be replaced and unwanted applications can be removed.