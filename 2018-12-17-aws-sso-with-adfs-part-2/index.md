# AWS SSO With ADFS - Part 2


I'd assume ADFS has already been setup correctly.

We'll only concentrate on `How to setup ADFS side and AWS side for SSO with SAML`

<!--more-->

- - -

## Design & Implementation

### AWS Side

We will be using `Identity providers` - `SAML` type - with Provider name as -> `ADFS`

Metadata Document can be granted from URL: `https://<your_adfs_url>/FederationMetadata/2007-06/FederationMetadata.xml`

We need to create `Role` for all kind-like users in the same AD group to be mapped in AWS.

Let's create a role called `<accountName>-admin`, like `sandbox-admin`, and binds with "Administrators" permission with it.

- Select `SAML 2.0 federation`
- Select `ADFS` previously created as the provider
- Select the way you want users to access the console or resources, here I select `Allow programmatic and AWS Management Console access`
- Next, permission, we tick `Administrators`
- Next, Next, Role name: `sandbox-admin`

Till here, all AWS side is finished.


### On-prem AD side

The main steps are pretty much the same as: https://aws.amazon.com/blogs/security/aws-federated-authentication-with-active-directory-federation-services-ad-fs/

However, I made some changes to adopt with more features:

As users will be granting permissions by joining to an AD group, so we will need to create a group before doing anything: `AWSGlobal-123456789012-sandbox-admin`

- AWSGlobal: hard-coded value as prefix showing what is this for. <- by having this one, you will be able to map to different region, like China or Gov
- 123456789012: AWS Account ID in number. <- by changing this bit, you will be able to map to different accounts.
- sandbox-admin: role name (created in AWS side). <- by changing this, you will be able to login to different role with different permission.

In ADFS configuration, `Role Attributes` should be following:

```cmd
c:[Type == "http://temp/variable", Value =~ "(?i)^AWSGlobal-([\d]{12})-([a-zA-Z]*)-"]
 => issue(Type = "https://aws.amazon.com/SAML/Attributes/Role", Value = RegExReplace(c.Value, "AWSGlobal-([\d]{12})-", "arn:aws:iam::$1:saml-provider/ADFS,arn:aws:iam::$1:role/"));
```

Leave the rest the same as above link.

Then try to login from ADFS portal, you will be redirect to AWS Console with correct permission as `sandbox-admin`. 


Have fun there. 

R
