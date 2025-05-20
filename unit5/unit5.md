# Unit 5






## Lecture Notes

### This Week

Even if we have an air-gapped system, we still need a way to load software on it.
We need to make sure we have a secure repo for where software comes from.

Building Repos and Enterprise patching

### Overview

Why is control important for security engineering?
  - What is fit for use?
    - "it is a car, check" "it has 4 wheels, check"
  - What is fit for warranty?
    - "when i turn the key, does it turn on the 5001st time"
    - do you bet your weekend on it?
    - i need to ensure nobody changes the things we checked.
      - maybe something sounds good, but libraries deprecated, or this has a vuln
      - testing validatese
    - air-gapped means, since its isolated from public network, we dont have to 
    worry about updates going awry because it can't connect to those default repos
Why do we create a repo locally?
  - we can repo air-gap
How do we create a repo locally?
How do we share a repo over the network?
How do we configure our repos?
  - how is `dnf -y update` different on one server vs 1000 other servers
How does enterprise patching differ from one off patching of systems?
What is a lifecycle in engineering and how does it relate to patching?




https://network.nvidia.com/products/infiniband-drivers/linux/mlnx_ofed/
drivers for connectx software for infiniband-drivers


harden apache and see how it effects the ability to use our repos we build


ss -ntulp | grep x80
lsof -i :80

curl 127.0.0.1
curl hostname


Apache directives





