# Qwiklabs Deploy to Kuberenetes in Google Cloud Challenge Lab

Step by Step Guide to solve this challenge. Can refer my YoutTube video far the same : https://youtu.be/SmbBhmOeh8c

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

