# Shell-Script-for-AWS-IAM-Management


## Introduction
Efficient management of AWS Identity and Access Management (IAM) resources is crucial for maintaining scalability, security, and operational efficiency in cloud environments. As organizations grow, manual IAM management can become tedious and error-prone. Automating these tasks through scripting provides a scalable and reliable solution.

This project focuses on enhancing a shell script to automate key IAM tasks, including creating users, organizing them into groups, attaching administrative policies, and ensuring consistent permissions. By streamlining these processes, the project simplifies IAM management, reduces manual effort, and supports organizational scalability while adhering to cloud management best practices.

## Project Objectives
The objective of this project is to enhance a shell script to automate the management of AWS Identity and Access Management (IAM) resources. This includes:
- Creating IAM users from a predefined list.
- Organizing them into an admin group.
- Attaching AWS-managed administrative policies to the group.
- Ensuring all users are assigned the necessary permissions.

By streamlining these processes, the project aims to simplify IAM operations, improve efficiency, and support the scalability of AWS environments for growing organizations.

## Project Requirements
Extend your shell scripting capabilities by creating more functions that extend the "aws_cloud_manager.sh" script. The following objectives need to be met:
1. **Define IAM Usernames Array**: Store the names of the IAM users in an array for easy iteration during user creation.
2. **Create IAM Users**: Iterate through the IAM usernames array and create IAM users for each employee using AWS CLI commands.
3. **Create IAM Group**: Define a function to create an IAM group named "admin" using the AWS CLI.
4. **Attach Administrative Policy to Group**: Attach an AWS-managed administrative policy (e.g., "AdministratorAccess") to the "admin" group to grant administrative privileges.
5. **Assign Users to Group**: Iterate through the array of IAM usernames and assign each user to the "admin" group using AWS CLI commands.

## Step 1: AWS CLI Installation and Configuration
1. **Spin up an EC2 instance and SSH into it**:
    ```bash
    ssh -i "devops.pem" ubuntu@ec2-3-73-159-115.eu-central-1.compute.amazonaws.com
    ```

2. **Update the terminal and install AWS CLI**:
    ```bash
    sudo apt-get update
    curl "https://awscli.amazonaws.com/awscli-exe-linux-x86_64.zip" -o "awscliv2.zip"
    unzip awscliv2.zip
    sudo ./aws/install
    ```

3. **Verify the CLI installation**:
    ```bash
    aws --version
    ```

4. **If the EC2 instance does not have an IAM role with the required permissions, configure the AWS CLI**:
    ```bash
    aws configure
    ```

## Step 2: Create the IAM Script `aws_cloud_manager.sh`
1. **Create the script**:
    ```bash
    touch aws_cloud_manager.sh
    ```

2. **Open the script in a text editor**:
    ```bash
    sudo vi aws_cloud_manager.sh
    ```

3. **Add the following script implementation**:

    ```bash
    #!/bin/bash

    # AWS IAM Management Script
    # Automates the creation of IAM users, groups, and permission assignments.

    # Define IAM usernames for each group
    ADMIN_USERS=("John_Musa")
    DEVELOPER_USERS=("Emeka_Patrick" "Osita_Ani")

    # Function to create IAM users
    create_iam_users() {
        echo "Creating IAM users..."
        for user in "${ADMIN_USERS[@]}" "${DEVELOPER_USERS[@]}"; do
            aws iam create-user --user-name "$user" > /dev/null 2>&1
            if [ $? -eq 0 ]; then
                echo "Created IAM user: $user"
            else
                echo "Failed to create IAM user: $user (may already exist)"
            fi
        done
    }

    # Function to create an IAM group
    create_iam_group() {
        local group_name=$1
        echo "Creating IAM group: $group_name..."
        aws iam create-group --group-name "$group_name" > /dev/null 2>&1
        if [ $? -eq 0 ]; then
            echo "Created IAM group: $group_name"
        else
            echo "Failed to create IAM group: $group_name (may already exist)"
        fi
    }

    # Function to attach a policy to a group
    attach_policy_to_group() {
        local group_name=$1
        local policy_arn=$2
        echo "Attaching policy to group: $group_name..."
        aws iam attach-group-policy --group-name "$group_name" --policy-arn "$policy_arn" > /dev/null 2>&1
        if [ $? -eq 0 ]; then
            echo "Attached policy $policy_arn to group: $group_name"
        else
            echo "Failed to attach policy to group: $group_name"
        fi
    }

    # Function to assign IAM users to a group
    assign_users_to_group() {
        local group_name=$1
        shift
        local users=("$@")
        echo "Assigning users to group: $group_name..."
        for user in "${users[@]}"; do
            aws iam add-user-to-group --user-name "$user" --group-name "$group_name" > /dev/null 2>&1
            if [ $? -eq 0 ]; then
                echo "Added user $user to group: $group_name"
            else
                echo "Failed to add user $user to group: $group_name (may already be assigned)"
            fi
        done
    }

    # Main function to execute all tasks
    main() {
        # Define group names
        ADMIN_GROUP="Admin"
        DEVELOPER_GROUP="Developers"
        # Define policies
        ADMIN_POLICY_ARN="arn:aws:iam::aws:policy/AdministratorAccess"
        DEVELOPER_POLICY_ARN="arn:aws:iam::aws:policy/PowerUserAccess"
        # Create IAM users
        create_iam_users

        # Create IAM groups
        create_iam_group "$ADMIN_GROUP"
        create_iam_group "$DEVELOPER_GROUP"

        # Attach policies to groups
        attach_policy_to_group "$ADMIN_GROUP" "$ADMIN_POLICY_ARN"
        attach_policy_to_group "$DEVELOPER_GROUP" "$DEVELOPER_POLICY_ARN"

        # Assign users to groups
        assign_users_to_group "$ADMIN_GROUP" "${ADMIN_USERS[@]}"
        assign_users_to_group "$DEVELOPER_GROUP" "${DEVELOPER_USERS[@]}"
    }

    # Execute the main function
    Main
    ```

## Step 3: Make the Script Executable and Run the Script
1. **Make the script executable**:
    ```bash
    chmod +x aws_cloud_manager.sh
    ```

2. **Execute the script**:
    ```bash
    ./aws_cloud_manager.sh
    ```

## Conclusion
This project successfully demonstrates the automation of AWS IAM resource management using a shell script. By leveraging the AWS CLI, the script simplifies the process of creating IAM users, groups, and managing permissions, reducing manual effort and the potential for errors.

The script effectively handles the creation of IAM users, groups (Admin and Developers), the assignment of users to their respective groups, and the attachment of appropriate policies, such as AdministratorAccess for the Admin group. This approach ensures scalability, security, and consistency in managing access control for cloud environments.

Through this project, we have showcased how automation can enhance operational efficiency, improve security compliance, and enable faster onboarding of team members in an organization using AWS. This script serves as a foundation for more advanced automation workflows and demonstrates the importance of Infrastructure as Code (IaC) in modern cloud operations.
