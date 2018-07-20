# Spark_Cluster_on_Vagrant
  Vagrant provides a simple and fast way to build a standlone culster on your laptop or pc. 
  1.  To install vagrant on ubuntu is easy:
  ```console
  sudo apt install virtualbox vagrant
  ```
  2. download spark package [here](https://spark.apache.org/downloads.html), named "spark.tgz"
  
  3. Create *Vagrantfile* to give a defination of the cluster, such as how many notes and their names.
  
  In the file *[Vagrantfile](https://github.com/neoaksa/Spark_Cluster_on_Vagrant/blob/master/Spark_Cluster_Vagrant/Vagrantfile)*,we create a cluster with one host and two nodes. Blew is some core snippets:
  ```console
  deploy.vm.provider
  ```
  Vagrant is used for setting up a virtual enviorment, which is not a virtual machine itself.so we need to give the provider which chould be "virtualbox", "libvirt".
  
  ```console
  v.name  //the name of vm
  v.memory // memory, Unit=MB
  v.cpus  //how many cpu to be used for this node
  ```
  ```console
  vm.box  //initial image
  vm.hostname //host name, which is used for ssh connection
  vm.network  // three types: forwarded_port, private_network(to build a cluster),public_network
  ```
  If we set a private network within a same network segment, then a cluster has been created.
  
  4. run `vagrant up` in your `vagrantfile` is. 
  5. SSH: `vagrant ssh hn0` to access host, then you can use `sudo su` to gain root role. Or Spark website: `http://10.1.0.4:8080`. `vagrant ssh config` to browse the configration of this culster.
  6. To test it
  ```console
  vagrant ssh hn0
  spark-submit --class org.apache.spark.examples.SparkPi ~/spark/examples/jars/spark-examples_2.11-2.2.1.jar 1000

  ```
  7. `vagrant halt` to stop vm, `vagrant halt hn0` to stop host, `vagrant destroy` to delete vm(type 4 y to delete all of them)
