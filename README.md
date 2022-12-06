# Bash-Script
#This is a bash script for Creating Google VPC and Compute engine 
#!/bin/bash

#apostlepauljack@gmail.com

#LAB NAME:
#Getting Started with VPC Networking and Google Compute Engine

#View the subnets
gcloud compute networks subnets list --network default && \

#View the routes
gcloud compute routes list --filter="network=default AND priority=1000"

#View the Firewall rules
gcloud compute firewall-rules list 

#Delete the Firewall rules
gcloud compute firewall-rules delete default-allow-icmp && \
gcloud compute firewall-rules delete default-allow-rdp && \
gcloud compute firewall-rules delete default-allow-ssh && \
gcloud compute firewall-rules delete default-allow-internal

#Delete the default network
gcloud compute networks delete default

#confirm the deletion of the default network

gcloud compute routes list && \
gcloud compute firewall-rules list

#Try to create a VM instance
gcloud compute instances create demo-vm --zone=us-central1-c --machine-type=f1-micro 


#Create an auto mode VPC network with Firewall rules
gcloud compute networks create mynetwork --subnet-mode=auto && \
gcloud compute firewall-rules create mynetwork-allow-custom --network mynetwork --allow all --source-ranges "0.0.0.0/0" && \
gcloud compute firewall-rules create mynetwork-allow-icmp --network mynetwork --allow icmp --source-ranges "0.0.0.0/0" && \
gcloud compute firewall-rules create mynetwork-allow-ssh --network mynetwork --allow tcp:22 --source-ranges "0.0.0.0/0" && \
gcloud compute firewall-rules create mynetwork-allow-rdp --network mynetwork --allow tcp:3389 --source-ranges "0.0.0.0/0"

#Create a VM instances in us-central1
gcloud compute instances create mynet-us-vm --network=mynetwork --zone=us-central1-c --machine-type=f1-micro

#Create a VM instances in europe-central2
gcloud compute instances create mynet-eu-vm --network=mynetwork --zone=europe-central2-a --machine-type=f1-micro

#Verify connectivity for the VM instances
gcloud compute ssh mynet-us-vm

ping -c 3 <Enter mynet-eu-vm internal IP here> && \

ping -c 3 <Enter mynet-eu-vm external IP here> && \

exit

#Remove the allow-icmp firewall rules
gcloud compute firewall-rules delete mynetwork-allow-icmp && \

gcloud compute ssh mynet-us-vm && \

ping -c 3 <Enter mynet-eu-vm internal IP here> && \

ping -c 3 <Enter mynet-eu-vm external IP here> && \

exit

#Remove the allow-custom firewall rules
gcloud compute firewall-rules delete mynetwork-allow-custom && \

gcloud compute ssh mynet-us-vm && \

ping -c 3 <Enter mynet-eu-vm internal IP here> && \

exit

#Remove the allow-ssh firewall rules
gcloud compute firewall-rules delete mynetwork-allow-ssh && \

gcloud compute ssh mynet-us-vm
