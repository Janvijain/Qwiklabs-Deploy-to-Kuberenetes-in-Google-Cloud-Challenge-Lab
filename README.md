# Qwiklabs Deploy to Kuberenetes in Google Cloud Challenge Lab

Step by Step Guide to solve this challenge. Can refer my YouTube video far the same : https://youtu.be/SmbBhmOeh8c

# Task 1: Create a Docker image and store the Dockerfile

Open Cloud Shell              
->  source <(gsutil cat gs://cloud-training/gsp318/marking/setup_marking.sh)              

->export PROJECT=[GCP Project ID]     

->gcloud source repos clone valkyrie-app --project=$PROJECT  

->ls

->cd valkyrie-app/  

-> cat > Dockerfile <<EOF                  
> FROM golang:1.10                 
> WORKDIR /go/src/app                
> COPY source .                   
> RUN go install -v                                  
> ENTRYPOINT ["app","-single=true","-port=8080"]                 
> EOF                            
         
->docker build -t valkyrie-app:v0.0.1 .                   

->docker images              

->ls                  

->cd ..              

->ls                  

->./step1.sh                
Check your progress

# Task 2: Test the created Docker image

->cd valkyrie-app/             
 
->ls                           

->docker run -p 8080:8080 valkyrie-app:v0.0.1 &                    

Click for Web preview -> Preview on port 8080                            
New terminal (+)                  
->ls                    

->cd marking/                  

->./step2.sh                     
Check your progress                

# Task 3: Push the Docker image in the Container Repository

In the first terminal            
->ls                       

->docker tag valkyrie-app:v0.0.1 gcr.io/$PROJECT/valkyrie-app:v0.0.1                     

->docker images                                          

->docker push gcr.io/$PROJECT/valkyrie-app:v0.0.1                          

Check your progress                   

# Task 4: Create and expose a deployment in Kubernetes

->ls                             

->cd k8s/                          

->ls                                       

->cat deployment.yaml                              

->ls                        

->cat service.yaml                             
 
->ls                         

->gcloud container clusters get-credentials valkyrie-dev --zone us-east1-d                                  

(You will get zone from Navigation menu->Kubernetes Engine ->Clusters)                              

->sed -i s#IMAGE_HERE#gcr.io/$PROJECT/valkyrie-app:v0.0.1#g k8s/deployment.yaml                                 

->ls                                                                                 

->vi deployment.yaml                                

->i                          
change value of image in containers                                
image: gcr.io/[GCP Project ID]/valkyrie-app:v0.0.1                               

Esc key                                                       

:wq                                                                                            
                                                                                
->gcloud container clusters get-credentials valkyrie-dev --zone us-east1-d                                                                
                               
->kubectl create -f ./deployment.yaml                                                                                                          

->kubectl create -f ./service.yaml                                      
     
Check your progress                                                                                                                                             


# Task 5: Update the deployment with a new version of valkyrie-app

->cd ..

->ls

->git merge origin/kurt-dev

->kubectl edit deployment valkyrie-dev

change value of image in containers
image: gcr.io/[GCP Project ID]/valkyrie-app:v0.0.2

spec:
replicas: 3
.
.
.
replicas: 3                (3rd last line)
Increase the replicas from 1 to 3

Esc key

:wq

->docker build -t valkyrie-app:v.0.0.2 .

->docker tag valkyrie-app:v.0.0.2 gcr.io/$PROJECT/valkyrie-app:v0.0.2

->docker images

->docker push gcr.io/$PROJECT/valkyrie-app:v0.0.2

Check your progress


# Task 6: Create a pipeline in Jenkins to deploy your app

Get the password with                                                      
->printf $(kubectl get secret cd-jenkins -o jsonpath="{.data.jenkins-admin-password}" | base64 --decode);echo                                   

->docker ps                                         

->docker kill [container id]                                               
 
You would have got container id by running "docker ps" command                                  

->docker ps                                      

->export POD_NAME=$(kubectl get pods --namespace default -l "app.kubernetes.io/component=jenkins-master" -l "app.kubernetes.io/instance=cd" -o jsonpath="{.items[0].metadata.name}")                                        
kubectl port-forward $POD_NAME 8080:8080 >> /dev/null &                                 
                 
Web Preview                                  
Sign In to Jenkins                               
user id = admin                                                     
password = generated by above command                                                  

On the Left menu                                        
Manage Jenkins                            
->Manage Credentials         
->Stores scoped to Jenkins->Domains                                            
	->Click global                                      
		->Add credentials                                         
			->Kind : Google Service Account from metadata                                 
			->OK                                                      
->Jenkins Home Page                                                       
	->New Item                                               
		->Item name : valkyrie-app                                  
		->Pipeline->OK                           
			->Definition : Pipeline script from SCM                                  
			SCM->Git                                     
			Repository URL: (Go to Cloud Shell run this command ->gcloud source repos list)                            
			Credentials : select you Project ID                                                   
			Save                             

Cloud Shell                                  
->sed -i "s/green/orange/g" source/html.go                              
->sed -i "s/YOUR_PROJECT/$PROJECT/g" Jenkinsfile                                       
->git config --global user.email "[Username]"                                  
->git config --global user.name "[GCP Project ID]"                              
->git add .                                                  
->git commit -m "green to orange"                           
->git push origin master                                     

Go to Jenkins Page                               
On the left menu -> Build Now                      


# Congratulation! you have successfully completed your challenge.
