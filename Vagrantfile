Vagrant.configure("2") do |config|

  config.vm.box = "debian/bookworm64"

  # =========================
  # CONFIG COMMUNE
  # =========================
  config.vm.provision "shell", inline: <<-SHELL
    # Création de l'utilisateur admin si inexistant
    if ! id admin >/dev/null 2>&1; then
        useradd -m -s /bin/bash admin
    fi

    # Création du dossier SSH
    mkdir -p /home/admin/.ssh

    # Ajout de la clé publique si absente
    grep -qxF "$(cat /vagrant/id-rda-infra.key.pub)" /home/admin/.ssh/authorized_keys 2>/dev/null || \
        cat /vagrant/id-rda-infra.key.pub >> /home/admin/.ssh/authorized_keys

    # Permissions
    chown -R admin:admin /home/admin/.ssh
    chmod 700 /home/admin/.ssh
    chmod 600 /home/admin/.ssh/authorized_keys

    # Sudo sans mot de passe
    echo "admin ALL=(ALL) NOPASSWD:ALL" > /etc/sudoers.d/admin
    chmod 440 /etc/sudoers.d/admin
  SHELL


  # =========================
  # VM TEST
  # =========================
  config.vm.define "test" do |test|
    test.vm.hostname = "app-dev"
    test.vm.network "private_network", ip: "192.168.56.10"

    test.vm.provider "virtualbox" do |vb|
      vb.name = "app-dev"
      vb.memory = 1024
      vb.cpus = 1
    end
  end

  # =========================
  # VM PROD
  # =========================
  config.vm.define "prod" do |prod|
    prod.vm.hostname = "app-prod1"
    prod.vm.network "private_network", ip: "192.168.56.11"

    prod.vm.provider "virtualbox" do |vb|
      vb.name = "app-prod1"
      vb.memory = 1024
      vb.cpus = 1
    end
  end

    # =========================
  # VM JENKINS
  # =========================
  config.vm.define "jenkins" do |jenkins|
    jenkins.vm.hostname = "vm-jenkins"
    jenkins.vm.network "private_network", ip: "192.168.56.20"
    jenkins.vm.network "forwarded_port", guest: 8080, host: 8081

    jenkins.vm.provider "virtualbox" do |vb|
      vb.name = "vm-jenkins"
      vb.memory = 2048
      vb.cpus = 2
    end
  end

end
