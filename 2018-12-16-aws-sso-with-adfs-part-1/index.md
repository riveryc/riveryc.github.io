# AWS SSO With ADFS - Part 1


I'm a lazy man, so always thinking to integrate things together to reduce anything to remember. 

AWS provides its own way to authenticate users via IAM module, however it's just another system to me, so as another credential to me to manage...

I guess any IT won't like it as too many places to create/delete/disable users, so does user... I think pretty much security will join the party too. 

<!--more-->

AWS provides another way to authenticate user via `Role`. 

What we need to do, is relaying on the SAML provider to authenticate our users in AD via ADFS portal, then map all users to the specific role in AWS account:

- Finance:
    - Mark
    - John
    - Susan
- Operation:
    - Jay
    - Peter
    - Tom

In AWS, we can create 2 roles for above groups:

- finance
- operation

Thus, as long as user are in the specified AD group, they are able to login to AWS with certain permission without remembering another credential.

- - -

# How it works:
<img src="/img/post/20181216/aws-sso-with-adfs.png" class="zoomable">

1. User accesses to ADFS web portal
2. User's credential is passed to Windows AD to get authenticated
3. Get the authentication response (SAML assertion) from AD and send back to user's end
4. User use this authentication response (SAML assertion) to post to AWS endpoint. AWS side will use `AssumeRoleWithSAML` with the "SAML assertion" to authorise user to the role specified.
5. AWS will return the Management Console back to user as the role specified.

Part 2 will introduce the strategy and how to set it up
