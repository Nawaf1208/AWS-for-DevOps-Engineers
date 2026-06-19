# AWS-for-DevOps-Engineers

![AWS](https://img.shields.io/badge/AWS-%23FF9900.svg?style=for-the-badge&logo=amazon-aws&logoColor=white)

![](AWS.png)  

## IAM

<details>
<summary><b><i>1.As you probably know at this point, it's not recommended to work with the root account in AWS. For this reason you are going to create a new account which you'll use regularly as the admin account.
  
- Create a user with password credentials
- Add the newly created user to a group called "admin" and attach to it the policy called "Administrator Access"
- Make sure the user has a tag called with the key Role and the value DevOps
  
</i></b></summary>

$\color{green}{\text{Answer}}$

1. Go to the AWS IAM service

2. Click on "Users" in the right side menu (right under "Access Management")

3. Click on the button "Add users"

4. Insert the user name (e.g. mario)

5. Select the credential type: "Password"

6. Set console password to custom and click on "Next"

7. Click on "Add user to group"

8. Insert "admin" as group name

9. Check the "AdministratorAccess" policy and click on "Create group"

10. Click on "Next: Tags"

11. Add a tag with the key Role and the value DevOps

12. Click on "Review" and then create on "Create user"

13. Solution using Terraform
    ```Terraform     
    resource "aws_iam_group_membership" "team" {
      name = "tf-testing-group-membership"

      users = [
        aws_iam_user.newuser.name,

      ]

      group = aws_iam_group.admin.name
    }

    resource "aws_iam_group_policy_attachment" "test-attach" {
      group      = aws_iam_group.admin.name
      policy_arn = "arn:aws:iam::aws:policy/AdministratorAccess"
    }
    resource "aws_iam_group" "admin" {
      name = "admin"
    }

    resource "aws_iam_user" "newuser" {
      name = "newuser"
      path = "/system/"

      tags = {
        Role = "DevOps"
      }
    }
    ```

</details>
