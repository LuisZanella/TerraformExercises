# Terraform five minutes tutorial

## Terraform Commands
<table>
    <tr>
        <td> Terraform login </td>
        <td> Login in hashicorp.. </td>
    </tr>
    <tr>
        <td> Terraform console </td>
        <td> Enter into terraform console. Use the Terraform console to inspect variables etc.. </td>
    </tr>
    <tr>
        <td> Terraform init </td>
        <td> Create a new configuration — or check out an existing configuration  from version control — you need to  initialize the directory </td>
    </tr>
    <tr>
        <td> Terraform fmt </td>
        <td> Automatically updates configurations in the current directory for readability and consistency.
        </td>
    </tr>
    <tr>
        <td> Terraform validate </td>
        <td> You can also make sure your configuration is syntactically valid and internally consistent </td>
    </tr>
    <tr>
        <td> Terraform apply </td>
        <td> 
            Apply the configuration
                </br> 
            if fails: https://developer.hashicorp.com/terraform/tutorials/aws-get-started/aws-build#troubleshooting
        </td>
    </tr>
    <tr>
        <td> Terraform output </td>
        <td> Terraform prints output values to the screen when you apply your configuration </td>
    </tr>
    <tr>
        <td> Terraform show </td>
        <td> Terraform stores the IDs and properties of the resources it manages in this file, so that it can update or destroy those resources going forward. Inspect the current state using this command </td>
    </tr>
    <tr>
        <td> Terraform state list </td>
        <td> List of the resources in your project's state. </td>
    </tr>
    <tr>
        <td> Terraform destroy </td>
        <td> Command terminates resources managed by your Terraform project. It does not destroy resources running elsewhere that are not managed by the current Terraform project. </td>
    </tr>
    <tr>
        <td> Terraform apply -var "instance_name=YetAnotherName" </td>
        <td> Setting variables via the command-line will not save their values. Notice that the command will fail and return the error message specified in the validation block. </td>
    </tr>
    <tr>
        <td> Terraform apply -var-file "../tacos.tfvars" </td>
        <td> Terraform automatically loads all files in the current directory with the exact name <em>terraform.tfvars</em> || <em>terraform.tfvars.json</em> or matching <em>*.auto.tfvars</em> || <em>*.auto.tfvars.json</em>. You can also use the -var-file flag to specify other files by name. </td>
    </tr>
     
</table>

## Terraform types

### Terraform root types
<table>
    <tr>
        <td> number </td>
        <td> 10 //Note: Terraform also interprets the number if is inside of a string like: "10" == 10 </td>
    </tr>
    <tr>
        <td> bool </td>
        <td> true | false
        </td>
    </tr>
    <tr>
        <td> string </td>
        <td> "String" </td>
    </tr>
</table>

## Terraform collection types
<table>
    <tr>
        <td> list(TYPE) </td>
        <td>
            A sequence of values of the same type. 
        </td>
    </tr>
    <tr>
        <td> map(TYPE) </td>
        <td> 
            A lookup table, matching keys to values, ALL OF THE SAME TYPE!!
        </td>
    </tr>
    <tr>
        <td> set(TYPE) </td>
        <td> 
            An unordered collection of unique values, ALL OF THE SAME TYPE!!
        </td>
    </tr>
        <td> list(map) </td>
        <td> 
            COMPLEX TYPES like list(list) and list(map).
        </td>
    </tr>
</table>

## Terraform structural types
<table>
    <tr>
        <th> <em>Those types require a schema as an argument, to specify which types are allowed for which elements</em>
        </th>
        <th><em> </br>
            variable "with_optional_attribute"{</br>
                type = object({</br>
                    a = string </br>
                    b = optional(string) </br>
                })</br>
            })</br></em>
        </th>
    </tr>
    <tr>
        <td> tuple </td>
        <td>
            tuple([string, number, bool])
        </td>
    </tr>
    <tr>
        <td> object </td>
        <td> 
            {</br>
                name = "jhon"</br>
                age = 32 </br>
            }<br/>
        </td>
    </tr>
</table>

## Variables on cli
```
terraform apply -var="image_id=ami-abc123"
terraform apply -var='image_id_list=["ami-abc123","ami-def456"]' -var="instance_type=t2.micro"
terraform apply -var='image_id_map={"us-east-1":"ami-abc123","us-east-2":"ami-def456"}'
```
## Variable Definitions (.tfvars) Files
```
terraform apply -var-file="testing.tfvars"
```
## Environment variables (TF_VAR_*) 
<em>(Terraform matches the variable name exactly as given in configuration)</em>
```
export TF_VAR_image_id=ami-abc123
terraform plan
```
<em>For convenience, Terraform defaults to interpreting -var and environment variable values as literal strings, which need only shell quoting, and no special quoting for Terraform. For example, in a Unix-style shell:</em>
```
export TF_VAR_image_id='ami-abc123'
export TF_VAR_availability_zone_names='["us-west-1b","us-west-1d"]'
```

## Variable Definition Precedence
<em>The above mechanisms for setting variables can be used together in any combination. If the same variable is assigned multiple values, Terraform uses the last value it finds, overriding any previous values. Note that the same variable cannot be assigned multiple values within a single source.

Terraform loads variables in the following order, with later sources taking precedence over earlier ones:</em>

1. Environment variables
2. The terraform.tfvars file, if present.
3. The terraform.tfvars.json file, if present.
4. Any *.auto.tfvars or *.auto.tfvars.json files, processed in lexical order of their filenames.
5. Any -var and -var-file options on the command line, in the order they are provided. (This includes variables set by a Terraform Cloud workspace.)

## Variable validation
Use variable validation to restrict the possible values for the project and environment tags.
```
    validation {
        condition     = length(var.resource_tags["project"]) <= 16 && length(regexall("[^a-zA-Z0-9-]", var.resource_tags["project"])) == 0
        error_message = "The project tag must be no more than 16 characters, and only contain letters, numbers, and hyphens."
    }
```
## Terraform common functions
 - slice(list, startindex, endindex)
```
    > slice(["a", "b", "c", "d"], 1, 3)
        [ "b", "c", ]
    > slice(var.private_subnet_cidr_blocks, 0, 3)
        tolist([ "10.0.101.0/24", "10.0.102.0/24", "10.0.103.0/24", ])
 ```
 - substr(string, offset, length)
```
    > substr("🤔🤷", 0, 1)
        🤔
    > substr("hello world", -5, -1)
        world
    > substr("hello world", 6, 10)
        world
 ```
 - regexall()
 ```
    validation {
        condition     = length(var.resource_tags["project"]) <= 16 && length(regexall("[^a-zA-Z0-9-]", var.resource_tags["project"])) == 0
        error_message = "The project tag must be no more than 16 characters, and only contain letters, numbers, and hyphens."
    }
 ```

 
