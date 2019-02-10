# -*- mode: ruby -*-
# vi: set ft=ruby :

Vagrant.configure("2") do |config|

  config.ssh.insert_key = false

# centios_managerサーバ
  # OS設定
  config.vm.define "centos_manager" do |centos_manager|
    centos_manager.vm.box = config.vm.box = "centos/7"
    centos_manager.vm.network "private_network", ip: "192.168.33.101"
    centos_manager.vm.provision :hosts, :sync_hosts => true
    #centos_manager.vm.synced_folder "./shared/centos_manager", "/home/vagrant/shared", owner: "vagrant", group: "vagrant"

    # 仮想マシンの設定
    centos_manager.vm.provider "vb" do |vb|
      vb.memory = 1024
      vb.cpus = 2
      vb.name = "centos_manager"
    end

    # プロビジョニング
    centos_manager.vm.provision "shell", privileged: false, inline: <<-SHELL
      # firewalld, SElinux の無効化
      sudo systemctl stop firewalld
      sudo systemctl disable firewalld
      sudo sed -i -e 's/SELINUX=enforcing/SELINUX=disabled/' /etc/selinux/config
    SHELL
    
    # 仮想マシンの再起動
    centos_manager.vm.provision :reload

    centos_manager.vm.provision "shell", privileged: false, inline: <<-SHELL
      # Hinemos 必須パッケージのインストール
      sudo yum install -y java-1.8.0-openjdk rsyslog unzip vim-common wget vlgothic-p-fonts
      sudo yum clean all

      # snmpd のインストール
      sudo yum install -y net-snmp net-snmp-utils
      sudo sed -i -e '$aview    systemview      included   .1.3.6.1' /etc/snmp/snmpd.conf
      sudo systemctl start snmpd
      sudo systemctl enable snmpd

      # Hinemos マネージャーのインストール
      sudo rpm -ivh https://github.com/hinemos/hinemos/releases/download/v6.1.2/hinemos-6.1-manager-6.1.2-1.el7.x86_64.rpm
      sudo systemctl start hinemos_manager
      sudo systemctl enable hinemos_manager

      # Hinemos web クライアントのインストール
      sudo rpm -ivh https://github.com/hinemos/hinemos/releases/download/v6.1.2/hinemos-6.1-web-6.1.2-1.el7.x86_64.rpm
      sudo sed -i -e 's/en_US/ja_JP/g' /opt/hinemos_web/conf/hinemos_web.cfg
      sudo systemctl start hinemos_web
      sudo systemctl enable hinemos_web

    SHELL

  end

# centos_nodeの作成
  # OS設定
  config.vm.define "centos_node" do |centos_node|
    centos_node.vm.box = config.vm.box = "centos/7"
    centos_node.vm.network "private_network", ip: "192.168.33.102"
    centos_node.vm.provision :hosts, :sync_hosts => true
    #centos_node.vm.synced_folder "./shared/centos_node", "/home/vagrant/shared", owner: "vagrant", group: "vagrant"

    # 仮想マシンの設定
    centos_node.vm.provider "vb" do |vb|
      vb.memory = 1024
      vb.cpus = 2
      vb.name = "centos_node"
    end

    # プロビジョニング
    centos_node.vm.provision "shell", privileged: false, inline: <<-SHELL
      # firewalld, SElinux の無効化
      sudo systemctl stop firewalld
      sudo systemctl disable firewalld
      sudo sed -i -e 's/SELINUX=enforcing/SELINUX=disabled/' /etc/selinux/config
    SHELL

    # 仮想マシンの再起動
    centos_node.vm.provision :reload

    centos_node.vm.provision "shell", privileged: false, inline: <<-SHELL
      # Hinemos 必須パッケージのインストール
      sudo yum install -y java-1.8.0-openjdk rsyslog unzip vim-common wget
      sudo yum clean all

      # snmpd のインストール
      sudo yum install -y net-snmp net-snmp-utils
      sudo systemctl start snmpd
      sudo systemctl enable snmpd

      # Hinemos エージェントのインストール
      sudo HINEMOS_MANAGER=192.168.33.101 rpm -ivh https://github.com/hinemos/hinemos/releases/download/v6.1.2/hinemos-6.1-agent-6.1.2-1.el.noarch.rpm
      sudo systemctl start hinemos_agent
      sudo systemctl enable hinemos_agent

    SHELL

    # 仮想マシンの再起動
    centos_node.vm.provision :reload
  
  end


end
