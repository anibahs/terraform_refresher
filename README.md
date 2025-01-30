# Terraform Interview Notes

## **1. Installation**
- **Install Terraform**:
  - Follow the official [Terraform installation guide](https://learn.hashicorp.com/tutorials/terraform/install-cli).
  - Use package managers like `brew` (macOS) or `choco` (Windows) for easier installation.
- **VSCode Extension**:
  - Install the [HashiCorp Terraform VSCode extension](https://marketplace.visualstudio.com/items?itemName=HashiCorp.terraform) for syntax highlighting, autocompletion, and validation.

---

## **2. Prerequisites**
- **Cloud Accounts**:
  - Create accounts for **AWS**, **Azure**, and **GCP** for hands-on labs.
- **CLI Tools**:
  - Install and configure:
    - **AWS CLI**
    - **Azure CLI**
    - **Google Cloud SDK**
- **Other Accounts**:
  - **Spotify** and **Minecraft** licenses for specific labs.

---

## **3. Getting Started**
### **Key Concepts**
- **`main.tf`**:
  - Main configuration file for Terraform.
- **AWS Provider**:
  - Add the AWS provider block to `main.tf`.
  - Example:
    ```hcl
    provider "aws" {
      region = "us-east-1"
    }
    ```
- **AWS Credentials**:
  - Generate AWS IAM credentials (access key and secret key).
  - Configure using `~/.aws/credentials` or environment variables.
- **Terraform Commands**:
  - **`terraform init`**: Initializes the Terraform project and downloads providers.
  - **`terraform fmt`**: Formats Terraform files for consistency.
  - **`terraform validate`**: Validates the configuration syntax.
  - **`terraform plan`**: Generates an execution plan.
  - **`terraform apply`**: Applies the changes to create/update resources.
  - **`terraform destroy`**: Destroys all resources managed by Terraform.

---

## **4. Variables and Outputs**
- **Input Variables**:
  - Define variables in `variables.tf`.
  - Example:
    ```hcl
    variable "instance_type" {
      type    = string
      default = "t2.micro"
    }
    ```
- **Outputs**:
  - Define outputs in `outputs.tf`.
  - Example:
    ```hcl
    output "instance_ip" {
      value = aws_instance.my_instance.public_ip
    }
    ```
- **Variable Files**:
  - Use `terraform.tfvars` or `-var-file` to pass variable values.
  - Environment variables: Prefix with `TF_VAR_` (e.g., `TF_VAR_instance_type`).

## **3. Variable Specification Precedence**
Terraform allows you to specify variables in multiple ways. The **precedence order** determines which value is used when multiple sources are defined.

### **Precedence Order (Highest to Lowest)**
1. **`-var` and `-var-file` Flags**:
   - Values passed via the command line (`-var`) or variable files (`-var-file`) take the highest precedence.
   - Example:
     ```bash
     terraform apply -var="instance_type=t2.large"
     ```
2. **`terraform.tfvars`**:
   - Automatically loaded if present in the root module.
3. **`*.auto.tfvars` Files**:
   - Automatically loaded in alphabetical order.
4. **Environment Variables (`TF_VAR_`)**:
   - Set environment variables prefixed with `TF_VAR_`.
   - Example:
     ```bash
     export TF_VAR_instance_type="t2.micro"
     ```
5. **Default Values**:
   - If no value is provided, Terraform uses the default value defined in the `variable` block.

### **Example**
```hcl
# variables.tf
variable "instance_type" {
  type    = string
  default = "t2.micro"
}
```

- If you run:
  ```bash
  terraform apply -var="instance_type=t2.large"
  ```
  - The value `t2.large` will be used (highest precedence).
- If no value is provided, the default `t2.micro` will be used.
  
---

## **5. Modules**
- **Create a Module**:
  - Organize reusable code into modules.
  - Example:
    ```hcl
    module "web_server" {
      source = "./modules/web_server"
    }
    ```
- **Publish a Module**:
  - Publish modules to the [Terraform Registry](https://registry.terraform.io/).

---

## **6. Provisioners**
- **Types**:
  - **`local-exec`**: Runs commands locally.
  - **`remote-exec`**: Runs commands on a remote resource.
  - **`file`**: Copies files to a remote resource.
  - **`connection`**: Defines connection details for remote provisioners.
- **Null Resource**:
  - Use `null_resource` with triggers to run provisioners based on changes.

---

## **7. Providers**
- **AWS**:
  - Provision resources like EC2, S3, and RDS.
- **Azure**:
  - Provision resources like VMs and storage accounts.
- **GCP**:
  - Provision resources like Compute Engine and Cloud Storage.
- **Spotify**:
  - Manage playlists using the Spotify provider.
- **Minecraft**:
  - Provision Minecraft servers.

---

## **8. Terraform State**
- **Commands**:
  - **`terraform state list`**: Lists resources in the state.
  - **`terraform state show`**: Shows details of a specific resource.
  - **`terraform state mv`**: Moves resources in the state.
- **State Management**:
  - Use **remote backends** (e.g., S3) for team collaboration.
  - Avoid manual edits to the state file.
## **2. State Management**
Terraform state (`terraform.tfstate`) is a critical component that tracks the current state of your infrastructure. Proper state management is essential for collaboration and avoiding conflicts.

### **Key Concepts**
- **State File**:
  - Stores the mapping between resources in your configuration and real-world resources.
  - Contains sensitive data (e.g., passwords, IPs), so it must be secured.
- **Remote State**:
  - Store the state file remotely (e.g., in S3, Terraform Cloud) for team collaboration.
  - Example (S3 backend):
    ```hcl
    terraform {
      backend "s3" {
        bucket = "my-terraform-state"
        key    = "path/to/state.tfstate"
        region = "us-east-1"
      }
    }
    ```
- **State Locking**:
  - Prevents multiple users from modifying the state simultaneously.
  - Supported by backends like S3 (with DynamoDB) and Terraform Cloud.
- **State Commands**:
  - **`terraform state list`**: Lists resources in the state.
  - **`terraform state show <resource>`**: Shows details of a specific resource.
  - **`terraform state mv <source> <destination>`**: Moves a resource in the state.
  - **`terraform state rm <resource>`**: Removes a resource from the state.

### **Best Practices**
- **Remote Backend**:
  - Always use a remote backend for team projects.
- **State Locking**:
  - Enable state locking to prevent conflicts.
- **State Security**:
  - Encrypt the state file and restrict access using IAM policies.
- **State Drift**:
  - Use `terraform refresh` to detect and reconcile drift between the state and real-world infrastructure.

---

## **9. Expressions and Functions**
- **String Templates**:
  - Use `${}` for interpolation.
- **For Expressions**:
  - Iterate over collections.
- **Splat Expressions**:
  - Access attributes from a list of resources.
- **Dynamic Blocks**:
  - Create nested blocks dynamically.

---

## **10. Resource Meta-Arguments**
- **`depends_on`**:
  - Explicitly define dependencies between resources.
- **`count`**:
  - Create multiple instances of a resource.
- **`for_each`**:
  - Create resources based on a map or set.
- **`lifecycle`**:
  - Control resource lifecycle (e.g., `prevent_destroy`).


## **1. `count` vs `for_each`**
Both `count` and `for_each` are meta-arguments used to create multiple instances of a resource or module. However, they have different use cases and behaviors.

### **`count`**
- **Purpose**: Creates multiple instances of a resource based on a numeric value.
- **Use Case**: Best for creating a fixed number of identical resources.
- **Example**:
  ```hcl
  resource "aws_instance" "web" {
    count         = 3
    ami           = "ami-123456"
    instance_type = "t2.micro"
  }
  ```
  - This creates **3 identical EC2 instances**.
- **Accessing Instances**:
  - Use `aws_instance.web[0]`, `aws_instance.web[1]`, etc., to access individual instances.
- **Limitations**:
  - If you remove an instance from the middle of the list (e.g., change `count = 3` to `count = 2`), Terraform will destroy and recreate resources, which can be disruptive.

### **`for_each`**
- **Purpose**: Creates multiple instances of a resource based on a **map** or **set**.
- **Use Case**: Best for creating resources with unique attributes or when you need more control over resource creation.
- **Example**:
  ```hcl
  resource "aws_instance" "web" {
    for_each = {
      "web1" = "ami-123456"
      "web2" = "ami-654321"
    }
    ami           = each.value
    instance_type = "t2.micro"
    tags = {
      Name = each.key
    }
  }
  ```
  - This creates **2 EC2 instances** with unique AMIs and tags.
- **Accessing Instances**:
  - Use `aws_instance.web["web1"]` or `aws_instance.web["web2"]` to access individual instances.
- **Advantages**:
  - More flexible than `count`.
  - Adding or removing items from the map/set does not disrupt other resources.

### **Key Differences**
| **Feature**       | **`count`**                              | **`for_each`**                          |
|--------------------|------------------------------------------|------------------------------------------|
| **Input Type**     | Number                                   | Map or Set                               |
| **Resource Access**| Index-based (`resource[0]`)              | Key-based (`resource["key"]`)            |
| **Flexibility**    | Less flexible                            | More flexible                            |
| **Use Case**       | Identical resources                      | Unique resources                         |


---

## **11. Terraform Cloud**
- **Workspaces**:
  - Manage environments (e.g., dev, prod) using workspaces.
- **Remote Backend**:
  - Migrate from local to remote backend for collaboration.
- **Sentinel**:
  - Enforce policies using Sentinel (e.g., cost control, security).

---

## **12. Advanced Topics**
- **Data Sources**:
  - Fetch data from existing resources.
- **Import**:
  - Import existing infrastructure into Terraform.
- **Troubleshooting**:
  - Use `TF_LOG` for debugging.
  - Check crash logs for errors.

---

## **13. Packer with Terraform**
- **Packer**:
  - Create machine images (e.g., AMI) for use in Terraform.
- **Integration**:
  - Use Packer to build images and Terraform to provision infrastructure.

---

## **14. Key Interview Topics**
- **Terraform Workflow**:
  - Understand the `init`, `plan`, `apply`, and `destroy` workflow.
- **State Management**:
  - Explain how Terraform state works and how to handle state conflicts.
- **Modules**:
  - Demonstrate how to create and use modules.
- **Provisioners**:
  - Discuss when and how to use provisioners.
- **Terraform Cloud**:
  - Explain the benefits of using Terraform Cloud for team collaboration.

---

## **15. Resources**
- **Terraform Registry**: [https://registry.terraform.io/](https://registry.terraform.io/)
- **Terraform Docs**: [https://www.terraform.io/docs/](https://www.terraform.io/docs/)
- **Packer Docs**: [https://www.packer.io/docs/](https://www.packer.io/docs/)

---

## **16. Key Interview Questions**
### **`count` vs `for_each`**
- **Q**: When would you use `count` instead of `for_each`?
  - **A**: Use `count` when creating a fixed number of identical resources. Use `for_each` when creating resources with unique attributes or when you need more control over resource creation.

### **State Management**
- **Q**: How do you handle Terraform state in a team environment?
  - **A**: Use a remote backend (e.g., S3, Terraform Cloud) with state locking to ensure collaboration and prevent conflicts. Encrypt the state file and restrict access using IAM policies.

### **Variable Precedence**
- **Q**: What is the order of precedence for Terraform variables?
  - **A**: The order is:
    1. `-var` and `-var-file` flags.
    2. `terraform.tfvars`.
    3. `*.auto.tfvars` files.
    4. Environment variables (`TF_VAR_`).
    5. Default values.
