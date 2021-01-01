# Containers and Docker
### What are Containers?
Containers are isolated executed environments. Libraries and applications are isolated within their own namespace on top of the existing OS and hardware. Containers are quicker than VMs because you don't need to deploy the full OS 
like you do with VMs; far fewer resources needed. They're consistent because all the libraries and application requirements are packaged within the container. Containers are deployed based on images and the images contain everything
we need to run the application so all we need to do is share the image to deploy the container onto different machines. 
### Use cases
They solve the "it worked on my machine" problem. Both the developer and the production machine work from the same container image. Migrating infrastructure that has been containerized lets us simply move the image over. If 
the infrastructure isn't containerized, we can pre-containerize it and then migrate it without worrying about needing to change our application due to an underlying hardware change. 