# Azure DevOps Pipeline Templates

Colección de templates reutilizables para pipelines de Azure DevOps.

## 📁 Estructura

```
templates/
├── install-terraform.yml       # Instalación de Terraform y herramientas AWS
├── terraform-init.yml          # Inicialización de Terraform con backend S3
├── terraform-plan.yml          # Ejecución de terraform plan
├── terraform-apply.yml         # Ejecución de terraform apply
├── terraform-destroy.yml       # Ejecución de terraform destroy
├── terraform-validate.yml      # Validación y formato de código Terraform
├── build-steps.yml             # Steps de build genéricos
├── test-steps.yml              # Steps de testing
├── docker-build.yml            # Build de imágenes Docker
├── k8s-deploy.yml              # Despliegue a Kubernetes
├── sonar-analysis.yml          # Análisis con SonarQube
└── connectivity-test.yml       # Pruebas de conectividad
```

## 🚀 Templates de Terraform

### install-terraform.yml

Instala Terraform y herramientas relacionadas (AWS CLI, kubectl).

**Parámetros:**
- `terraformVersion` (string, default: '1.13.0'): Versión de Terraform
- `installAwsCli` (boolean, default: false): Instalar AWS CLI v2
- `installKubectl` (boolean, default: false): Instalar kubectl

**Ejemplo:**
```yaml
- template: templates/install-terraform.yml@templates
  parameters:
    terraformVersion: '1.13.0'
    installAwsCli: true
    installKubectl: true
```

### terraform-init.yml

Inicializa Terraform con backend S3 y DynamoDB para state locking.

**Parámetros:**
- `workingDirectory` (string): Directorio de trabajo
- `backendS3Bucket` (string): Bucket S3 para el state
- `backendKey` (string): Key del archivo state
- `backendRegion` (string): Región AWS del backend
- `backendDynamoDBTable` (string): Tabla DynamoDB para locking
- `awsAccessKeyId` (string): AWS Access Key ID
- `awsSecretAccessKey` (string): AWS Secret Access Key
- `awsRegion` (string): Región AWS por defecto

**Ejemplo:**
```yaml
- template: templates/terraform-init.yml@templates
  parameters:
    workingDirectory: '$(System.DefaultWorkingDirectory)/iac/terraform'
    backendS3Bucket: '$(backendS3Bucket)'
    backendKey: '$(backendKey)'
    backendRegion: '$(awsRegion)'
    backendDynamoDBTable: '$(backendDynamoDBTable)'
    awsAccessKeyId: '$(AWS_ACCESS_KEY_ID)'
    awsSecretAccessKey: '$(AWS_SECRET_ACCESS_KEY)'
    awsRegion: '$(awsRegion)'
```

### terraform-validate.yml

Valida sintaxis y formato del código Terraform.

**Parámetros:**
- `workingDirectory` (string): Directorio de trabajo
- `strictFormatCheck` (boolean, default: false): Si es true, falla si el código no está formateado

**Ejemplo:**
```yaml
- template: templates/terraform-validate.yml@templates
  parameters:
    workingDirectory: '$(System.DefaultWorkingDirectory)/iac/terraform'
    strictFormatCheck: true  # Falla en PR si no está formateado
```

### terraform-plan.yml

Ejecuta terraform plan y genera un archivo de plan.

**Parámetros:**
- `workingDirectory` (string): Directorio de trabajo
- `planOutputFile` (string, default: 'tfplan'): Nombre del archivo de plan
- `awsAccessKeyId` (string): AWS Access Key ID
- `awsSecretAccessKey` (string): AWS Secret Access Key
- `awsRegion` (string): Región AWS
- `timeoutInMinutes` (number, default: 15): Timeout del plan

**Ejemplo:**
```yaml
- template: templates/terraform-plan.yml@templates
  parameters:
    workingDirectory: '$(System.DefaultWorkingDirectory)/iac/terraform'
    planOutputFile: 'tfplan'
    awsAccessKeyId: '$(AWS_ACCESS_KEY_ID)'
    awsSecretAccessKey: '$(AWS_SECRET_ACCESS_KEY)'
    awsRegion: '$(awsRegion)'
    timeoutInMinutes: 15
```

### terraform-apply.yml

Aplica un plan de Terraform.

**Parámetros:**
- `workingDirectory` (string): Directorio de trabajo
- `planFile` (string, default: 'tfplan'): Nombre del archivo de plan
- `awsAccessKeyId` (string): AWS Access Key ID
- `awsSecretAccessKey` (string): AWS Secret Access Key
- `awsRegion` (string): Región AWS
- `autoApprove` (boolean, default: true): Auto-aprobar apply

**Ejemplo:**
```yaml
- template: templates/terraform-apply.yml@templates
  parameters:
    workingDirectory: '$(System.DefaultWorkingDirectory)/iac/terraform'
    planFile: 'tfplan'
    awsAccessKeyId: '$(AWS_ACCESS_KEY_ID)'
    awsSecretAccessKey: '$(AWS_SECRET_ACCESS_KEY)'
    awsRegion: '$(awsRegion)'
```

### terraform-destroy.yml

Destruye infraestructura gestionada por Terraform.

**Parámetros:**
- `workingDirectory` (string): Directorio de trabajo
- `awsAccessKeyId` (string): AWS Access Key ID
- `awsSecretAccessKey` (string): AWS Secret Access Key
- `awsRegion` (string): Región AWS

**Ejemplo:**
```yaml
- template: templates/terraform-destroy.yml@templates
  parameters:
    workingDirectory: '$(System.DefaultWorkingDirectory)/iac/terraform'
    awsAccessKeyId: '$(AWS_ACCESS_KEY_ID)'
    awsSecretAccessKey: '$(AWS_SECRET_ACCESS_KEY)'
    awsRegion: '$(awsRegion)'
```