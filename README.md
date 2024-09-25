# GitLab CI + Terraform : Import d'un fichier CSV dans un bucket S3 avec déclenchement d'une lambda qui va lire le contenu du fichier et copier les données dans une base dynamodb


## Infrastructure

![image](https://github.com/user-attachments/assets/7904310a-9b48-4e0b-9568-c098a8b18463)


## Tools

Les outils qui ont été utilisés pour ce projet:

- [ ] [GitLab CI/CD](https://docs.gitlab.com/ee/topics/build_your_application.html), is a GitLab feature that lets you set up CI/CD pipelines. We used Gitlab CI/CD to manage the infrastructure deployment pipelines.
- [ ] [Terraform ](https://developer.hashicorp.com/terraform/docs), automates the provisioning of Cloud resources. We used Terraform to provision the bucket that will host the static website.
- [ ] [Amazon S3](https://docs.aws.amazon.com/AmazonS3/latest/userguide/Welcome.html), is a storage service. Amazon S3 is used to stock documents, pictures, logs etc.
- [ ] [Amazon Lambda](https://docs.aws.amazon.com/lambda/latest/dg/welcome.html), is a serverless computing service. We used Lambda to read the content of the csv file copied it in a Dynamodb data base.
- [ ] [Amazon DynamoDb](https://docs.aws.amazon.com/amazondynamodb/latest/developerguide/GettingStartedDynamoDB.html), is a no-sql Database used to store the content of the csv file (in this lab)

## Setup

1. **Creation de 2 bucket s3 pour Terraform **

Pour ce projet, nous allons avoir besoin de créer 2 buckets via la console AWS.

Le 1er bucket contiendra le fichier de configuration du backend (tfstate). Ce fichier est résume l'état actuel de votre infrastructure provisionnée par Terraform.
Gardez bien le nom de ce bucket que vous devrez renseigner dans votre code terraform. "<your-bucket-terraform-backend>"

```
  backend "s3" {
    region = "us-east-1"
    bucket = "<your-bucket-terraform-backend>"
    key    = "terraform.tfstate"
  }
```


2. **Création "manuelle" du Bucket s3 pour la lambda**

Ensuite il va falloir créer un autre Bucket s3 via la console qui contiendra l'artefact de la Lambda.
Dans le fichier variables.tf, retenez bien le nom de ce bucket qui vous servira pour sa déclaration coté Terraform.
Exemple : 
```
  variable "lambda_bucket_name" {
    type    = string
    default = "<your-bucket-xxxxx>"
  }
```

3. **Création via terraform du bucket s3 permettant d'uploader le fichier .csv**
Une fois le bucket déployé, il faudra créer manuellement un dossier nommé "raw-data".
Ce dossier sera renseigné dans le code terraform et l'upload du fichier .csv se fera dans celui-çi.

4. **Création via terraform d'une table DynamoDb**. Cette création a pour objectif de stocker les données du fichier csv qui seront lues et copiées par la lambda.
(A compléter par le bout de code de la DB)

5. **Configuration GitLab**
Dans le projet Gitlab, ajouter les variables d'environnement **AWS_ACCESS_KEY_ID** and **AWS_SECRET_ACCESS_KEY** sur la page _"Settings > CI/CD > Variables"
Ces variables vont autoriser Terraform à s'authentifier et communiquer avec les services AWS pour provisionner l'infrastructure.

6. **Deploiement**

Pour déployer l'infrastructure, cliquer sur le job **deploy** de votre pipeline Gitlab.

![image](https://github.com/user-attachments/assets/37090184-07a9-46dd-b7ed-d507cbfcbfbe)

7. Le job **destroy** est utilisé à la fin pour détruire toutes les ressources provisionnées par TERRAFORM sur le cloud AWS.
   **important**
   
8. Les buckets crées manuellement à savoir le bucket s3 pour le backend (tfstate) et pour la lambda devront être supprimés manuellement depuis la console AWS.
