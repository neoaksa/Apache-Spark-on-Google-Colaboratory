Vagrant.configure("2") do |config|
  N_WORKERS = 2

  config.vm.box = "ubuntu/xenial64"

  # head node:
  config.vm.define "hn0" do |node|
    node.vm.hostname = "hn0"
    node.vm.network "private_network", ip: "172.28.128.150"

    node.vm.provider "virtualbox" do |vb|
      vb.name = "hn0"
      vb.gui = false
      vb.memory = 2048
      vb.cpus = 2
    end

    node.vm.provision "shell", inline: <<-SHELL 
      apt-get install -y python-dev ntp default-jdk
      mkdir /home/vagrant/spark
      tar xf /vagrant/spark.tgz -C /home/vagrant/spark --strip 1
      echo "export SPARK_HOME=/home/vagrant/spark" >> /home/vagrant/.bashrc
      echo "export PATH=$PATH:/home/vagrant/spark/bin" >> /home/vagrant/.bashrc
      echo "spark.master spark://172.28.128.150:7077" >> /home/vagrant/spark/conf/spark-defaults.conf
      echo "SPARK_LOCAL_IP=172.28.128.150" >> /home/vagrant/spark/conf/spark-env.sh
      echo "SPARK_MASTER_IP=172.28.128.150" >> /home/vagrant/spark/conf/spark-env.sh
    SHELL

    node.vm.provision "shell", run: "always", inline: "/home/vagrant/spark/sbin/start-master.sh -h 172.28.128.150"
  end

  # worker nodes:
  (0..N_WORKERS-1).each do |i|
    config.vm.define "wn#{i}" do |node|
      node.vm.hostname = "wn#{i}"
      node.vm.network "private_network", ip: "172.28.128.20#{i}"

      node.vm.provider "virtualbox" do |vb|
        vb.name = "wn#{i}"
        vb.gui = false
        vb.memory = 4096
        vb.cpus = 2
      end

      node.vm.provision "shell", inline: <<-SHELL
        apt-get install -y python-dev ntp avahi-daemon default-jdk
        mkdir /home/vagrant/spark
        tar xf /vagrant/spark.tgz -C /home/vagrant/spark --strip 1
        echo "export SPARK_HOME=/home/vagrant/spark" >> /home/vagrant/.bashrc
        echo "export PATH=$PATH:/home/vagrant/spark/bin" >> /home/vagrant/.bashrc
        echo SPARK_LOCAL_IP=172.28.128.20#{i} >> /home/vagrant/spark/conf/spark-env.sh
        echo SPARK_MASTER_IP=172.28.128.150 >> /home/vagrant/spark/conf/spark-env.sh
      SHELL

      node.vm.provision "shell", run: "always", inline: "/home/vagrant/spark/sbin/start-slave.sh -h 172.28.128.20#{i} spark://172.28.128.150:7077"
    end
  end
end
