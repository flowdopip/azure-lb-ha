# azure-lb-ha
Azure Loadbalancer Reliability


# Create Azure Loadbalancer
```powershell

git clone https://github.com/MicrosoftDocs/mslearn-improve-app-scalability-resiliency-with-load-balancer.git
cd mslearn-improve-app-scalability-resiliency-with-load-balancer

bash create-high-availability-vm-with-sets.sh learn-c76493ad-5fe7-4777-b0c3-bdca83c57468

# create a public IP
az network public-ip create \
  --resource-group learn-c76493ad-5fe7-4777-b0c3-bdca83c57468 \
  --allocation-method Static \
  --name myPublicIP

# Create the loadbalancer
az network lb create \
  --resource-group learn-c76493ad-5fe7-4777-b0c3-bdca83c57468 \
  --name myLoadBalancer \
  --public-ip-address myPublicIP \
  --frontend-ip-name myFrontEndPool \
  --backend-pool-name myBackEndPool

# create the healh probe
az network lb probe create \
  --resource-group learn-c76493ad-5fe7-4777-b0c3-bdca83c57468 \
  --lb-name myLoadBalancer \
  --name myHealthProbe \
  --protocol tcp \
  --port 80

# LB rule to send traffic
az network lb rule create \
  --resource-group learn-c76493ad-5fe7-4777-b0c3-bdca83c57468 \
  --lb-name myLoadBalancer \
  --name myHTTPRule \
  --protocol tcp \
  --frontend-port 80 \
  --backend-port 80 \
  --frontend-ip-name myFrontEndPool \
  --backend-pool-name myBackEndPool \
  --probe-name myHealthProbe

# Connect the backend-pool
az network nic ip-config update \
  --resource-group learn-c76493ad-5fe7-4777-b0c3-bdca83c57468 \
  --nic-name webNic1 \
  --name ipconfig1 \
  --lb-name myLoadBalancer \
  --lb-address-pools myBackEndPool

az network nic ip-config update \
  --resource-group learn-c76493ad-5fe7-4777-b0c3-bdca83c57468 \
  --nic-name webNic2 \
  --name ipconfig1 \
  --lb-name myLoadBalancer \
  --lb-address-pools myBackEndPool

# Get the public IPs
echo http://$(az network public-ip show \
                --resource-group learn-c76493ad-5fe7-4777-b0c3-bdca83c57468 \
                --name myPublicIP \
                --query ipAddress \
                --output tsv)
```