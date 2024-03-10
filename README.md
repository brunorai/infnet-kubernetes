### Como utilizar:

1. **Requisitos**: 

    * AWS CLI 
    * Terraform 
    * Arquivos creds.txt preenchido com as credencias da AWS Academy
    * Alterar a quantidade nós master e workers no arquivo aws-provision.tf, seção de variáveis.

2. **Clone o repositório**: 

     ```bash
    git clone git@github.com:m-marcal/infnet-kubernetes.git
    ```

3. **Inicializar Terraform**: 

    ```bash
    terraform init 
    ```

4. **Deployment**:

    ```bash
    terraform apply --auto-approve # Não fazer em produção!
    ```

5. **(Opcional) Validar IPs dos nós Workers e Master**

    ```bash
    terraform output
    ```
### Uma vez que os servidores estejam provisionados, execute os próximos passos:

1. **Envie os arquivos YAML para o nó master**

    ```bash
    scp -i id_rsa kube-files/* ec2-user@{IP-DO-NÓ-MASTER}:/home/ec2-user/
    ```
2. **Conecte-se ao nó master**

    ```bash
    ssh -i id_rsa ec2-user@{IP-DO-NÓ-MASTER}
    ```

3. **Logar como Super User**

    ```bash
    sudo su -
    ```

4. **Instalar as dependências do Longhorn**

    ```bash
    kubectl apply -f https://raw.githubusercontent.com/longhorn/longhorn/v1.6.0/deploy/prerequisite/longhorn-iscsi-installation.yaml
    ```
    ```
    kubectl apply -f https://raw.githubusercontent.com/longhorn/longhorn/v1.6.0/deploy/prerequisite/longhorn-nfs-installation.yaml
    ```
5. **Instalar o Longhorn. Consulte a última versão disponível em https://longhorn.io/, Docs, e copie o comando da área de "quick installation"**

    ```bash
    kubectl apply -f https://raw.githubusercontent.com/longhorn/longhorn/v1.6.0/deploy/longhorn.yaml
    ```

### Por último, ainda no nó master, configure o projeto na seguinte ordem:

1. **Criar o namespace wp-projeto**

    ```bash
    kubectl apply -f project_namespace.yml 
    ```

2. **Criar configs do wordpress e mysql**

    ```bash
    kubectl apply -f conf_wodpress.yml
    ```
    ```bash
    kubectl apply -f conf_mysql.yaml 
    ```
3. **Criar secrets do wordpress e mysql**
    ```bash
    kubectl apply -f sec_wordpress_mysql.yml  
    ```
4. **Criar PVC do wordpress**
    ```bash
    kubectl apply -f pvc_wordpress.yaml
    ```
5. **Criar statefull set do mysql**
    ```bash
    kubectl apply -f stset_mysql.yaml 
    ```
6. **Criar Service do mysql**
    ```bash
    kubectl apply -f svc_mysql_headless.yml 
    ```
7. **Criar Deployment do Wordpress**
    ```bash
    kubectl apply -f dep_wordpress.yml
    ```
8. **Criar Service do Wordpress**
    ```bash
    kubectl apply -f svc_wordpress.yml 
    ```
9. **Por último, criar o ingress do wordpress**
    ```bash
    kubectl apply -f ing_wodpress.yml # editar ip do load balancer!
    ```



 
