# Adding Azure AD account sign-in to an ASP.NET Core

This is example of adding Azure AD account sign-in to an ASP.NET Core web app.

## Create Azure Active Directory web application

Use Bash and Azure CLI.

```
# Login without access to subscriptions.
az login --allow-no-subscriptions

# [EDIT HERE] Specify the application name.
DISPLAY_NAME=azuread-auth-example

# [EDIT HERE] Specify the redirect URL after login.
REPLY_URLS=https://localhost/signin-oidc

# [EDIT HERE] Specify the redirect URL after logout.
LOGOUT_URL=https://localhost/signout-oidc

# Get the 'User.Read' scope.
USER_READ_ID=$(az ad sp show --id 00000003-0000-0000-c000-000000000000 --query "oauth2Permissions[?value=='User.Read'].id" --output tsv)

# Create an App.
APP_ID=$(az ad app create --display-name $DISPLAY_NAME --reply-urls $REPLY_URLS --query 'appId' -o tsv)

# Apply URL after logout.
az ad app update --id $APP_ID --set logoutUrl=$LOGOUT_URL

# Apply 'User.Read' scope.
az ad app update --id $APP_ID --required-resource-accesses "[{
    \"resourceAppId\": \"00000003-0000-0000-c000-000000000000\",
    \"resourceAccess\": [{
        \"id\": \"$USER_READ_ID\",
        \"type\": \"Scope\"
    }]
}]"

# Grant consent by administrator.
az ad app permission admin-consent --id $APP_ID

```

## Applying Differences

Create ASP.NET Core web app.
```
dotnet new web -o AzureAdAuthExample
```

Apply the following differences.  
https://github.com/watertrans/AzureAdAuthExample/compare/7eb799c..a453627

Apply Azure AD and App information to appsettings.json.
```
"Domain": "example.com",
"TenantId": "11111111-1111-1111-1111-111111111111",
"ClientId": "22222222-2222-2222-2222-222222222222",
```
