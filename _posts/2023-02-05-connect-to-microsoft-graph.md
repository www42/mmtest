# Connect mit Connect-MgGraph -- TBD
Scopes sind die Permissions, die die App (hier 'Microsoft Graph PowerShell') hat. Zum Beispiel

"User.Read.All"  Allows the app to read the full set of profile properties, reports, and managers of other users in your organization, on behalf of the signed-in user.

"User.Read"   Allows you to sign in to the app with your organizational account and let the app read your profile. It also allows the app to read basic company information.

Find available scopes by object type e.g. user
Find-MgGraphPermission -SearchString user -PermissionType Delegated

Find rquired scopes by Graph uri
Find-MgGraphCommand -Uri "/users"
Find-MgGraphCommand -Uri "/users"      -ApiVersion v1.0 -Method POST   | % Permissions
Find-MgGraphCommand -Uri "/users/{id}" -ApiVersion v1.0 -Method DELETE | % Permissions


MS Graph Permissions werden akkumuliert. D.h. der SP 'Microsoft Graph PowerShell' sammelt immer mehr Berechtigungen, allerdings nur temporär (180 Tage?)


Connect-MgGraph

Get-MgContext
Get-MgContext | % Scopes





Connect-AzureAD
Get Service Principal
Get-AzureADServicePrincipal -All $true | ? DisplayName -eq 'Microsoft Graph PowerShell'

Get all delegated permissions for the service principal
$spOAuth2PermissionsGrants = Get-AzureADOAuth2PermissionGrant -All $true| Where-Object { $_.clientId -eq $sp.ObjectId }

$spOAuth2PermissionsGrants | fl *
$spOAuth2PermissionsGrants | % Scope
$spOAuth2PermissionsGrants | % ExpiryTime
(($spOAuth2PermissionsGrants | % ExpiryTime)) - (Get-Date)


Man kann die Permissions aber schon vor der ExpiryTime löschen

Remove all delegated permissions
$spOAuth2PermissionsGrants | ForEach-Object {
    Remove-AzureADOAuth2PermissionGrant -ObjectId $_.ObjectId
}



# Connect mit App Registration

Benefits:
    definierte scopes - nicht so akkumuliert wie beim interaktiven Arbeiten mit Connect-MgGraph
    Admin consent notwendig