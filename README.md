# Implantação de Aplicação Web com MySQL e Adminer no Kubernetes

 Este repositório contém os arquivos e a documentação para o trabalho prático da disciplina de Programação em Linux do curso de Engenharia de Computação do CEFET-MG   

 O projeto consiste na orquestração de uma aplicação de duas camadas, composta por um banco de dados **MySQL** e uma interface de gerenciamento web **Adminer**, utilizando os recursos fundamentais do Kubernetes   

## 📜 Estrutura do Repositório

* `documentacao.pdf`: O relatório técnico completo que descreve a arquitetura, os recursos do Kubernetes utilizados, os passos de implantação e a análise do projeto.
*  `mysql-secret.yaml`: Manifesto para criar um *Secret* que armazena a senha do banco de dados de forma segura   
*  `mysql-pv-pvc.yaml`: Manifesto que define o *PersistentVolume* (PV) e o *PersistentVolumeClaim* (PVC) para garantir a persistência dos dados do MySQL   
*  `mysql-deployment.yaml`: Manifesto para o *Deployment* do MySQL, que gerencia os pods do banco de dados  
*  `mysql-service.yaml`: Manifesto que expõe o MySQL dentro do cluster através de um *Service* do tipo ClusterIP  
*  `adminer-deployment.yaml`: Manifesto para o *Deployment* do Adminer, que gerencia os pods da interface web 
*  `adminer-service.yaml`: Manifesto que expõe o Adminer para acesso externo através de um *Service* do tipo NodePort   .

## 🏗️ Arquitetura da Solução

 A solução implementa uma arquitetura de microsserviços onde o Adminer (interface) e o MySQL (banco de dados) operam como componentes independentes e desacoplados no cluster Kubernetes   

* **Componente MySQL**:
    *  Gerenciado por um objeto *Deployment* para garantir que uma instância esteja sempre em execução  
    *  Exposto internamente no cluster por um *Service* do tipo **ClusterIP**, que atribui um nome DNS estável (`mysql-service`) para a comunicação com o Adminer  
    *  Os dados são persistidos através de um *PersistentVolumeClaim* (PVC) que se conecta a um *PersistentVolume* (PV)  
* **Componente Adminer**:
    *  Gerenciado por um *Deployment* que controla o ciclo de vida do contêiner   
    *  Exposto externamente por um *Service* do tipo **NodePort**, permitindo o acesso à interface web através do navegador   
* **Componentes de Suporte**:
    *  Um *Secret* é utilizado para armazenar de forma segura a senha de root do MySQL, que é injetada no contêiner como uma variável de ambiente  
## ✅ Pré-requisitos

Para executar este projeto, é necessário ter um ambiente Kubernetes local configurado.  A documentação foi baseada em um sistema operacional Ubuntu  

*  **Sistema Operacional**: Ubuntu 20.04 LTS ou superior 
*  **Virtualização**: Suporte à virtualização de hardware habilitado na BIOS   
*  **Recursos**: Mínimo de 2 CPUs, 4 GB de RAM e 20 GB de disco livre  
* **Software**:
    *  **Docker**: Instalado e em execução  
    *  **Minikube**: Para criar o cluster Kubernetes local   
    * **kubectl**: A ferramenta de linha de comando para interagir com o cluster.

## 🚀 Passos para Implantação

 Com o ambiente configurado e o cluster Minikube iniciado (`minikube start --driver=docker`   157, 158] ), execute os seguintes comandos na ordem especificada para implantar a aplicação   

1.  **Aplicar o Secret do MySQL:**
    ```bash
    kubectl apply -f mysql-secret.yaml
    ```  

2.  **Aplicar o PersistentVolume e o PersistentVolumeClaim:**
    ```bash
    kubectl apply -f mysql-pv-pvc.yaml
    ```
        

3.  **Aplicar o Deployment e o Service do MySQL:**
    ```bash
    kubectl apply -f mysql-deployment.yaml
    kubectl apply -f mysql-service.yaml
    ```
       

4.  **Aplicar o Deployment e o Service do Adminer:**
    ```bash
    kubectl apply -f adminer-deployment.yaml
    kubectl apply -f adminer-service.yaml
    ```
       

## 🔎 Verificação

 Após aplicar os manifestos, verifique se todos os recursos foram criados e estão em execução  

* **Listar todos os recursos:**
    ```bash
    kubectl get all
    ```
     
     *Procure pelos pods `mysql-deployment...` e `adminer-deployment-...` com o status `Running` e os services `mysql-service` e `adminer-service` devidamente listados*   .  *O número desejado de réplicas deve constar como `READY`*  

* **Verificar o status do PVC:**
    ```bash
    kubectl get pvc mysql-pvc
    ```
        
     *O status deve ser `Bound`, indicando que a solicitação de volume foi vinculada com sucesso*  .

## 💻 Acessando a Aplicação

1.   Para acessar a interface web do Adminer, execute o seguinte comando no seu terminal :
    ```bash
    minikube service adminer-service
    ```
      
     Este comando abrirá a URL da aplicação diretamente no seu navegador padrão  

2.   Na página de login do Adminer, utilize as seguintes credenciais  
    *  **Sistema**: `MySQL`    
    *  **Servidor**: `mysql-service` (o nome do Service do MySQL)   
    *  **Usuário**: `root`    
    *  **Senha**: `minha-senha-super-secreta` (a senha definida no `mysql-secret.yaml`)   

## 🔧 Solução de Problemas (Troubleshooting)

 Se encontrar problemas durante a implantação, consulte a seção "Dificuldades Encontradas e Soluções Aplicadas" na `documentacao.pdf`  . Os problemas mais comuns incluem:

*  **Pod Preso em `Pending`**: Geralmente causado por insuficiência de recursos (CPU/memória) no cluster   .  Use `kubectl describe pod <nome-do-pod>` para diagnosticar   
*  **Erro `ImagePullBackOff`**: Ocorre quando o Kubernetes não consegue baixar a imagem do contêiner   .  Verifique o nome da imagem no arquivo `.yaml` e sua conexão com a internet   
*  **Erro `CrashLoopBackOff`**: Indica um problema na aplicação dentro do contêiner  .  Para o MySQL, a causa comum é a ausência da variável de ambiente `MYSQL_ROOT_PASSWORD`  .  Use `kubectl logs <nome-do-pod>` para ver os erros  

## 👨‍💻 Autores

*  EDUARDO ALMEIDA   
*  JOÃO PAULO DA CUNHA FARIA    
