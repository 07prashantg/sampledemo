node {
    def mvnHome
    def remote = [:]
    remote.name = 'test'

    remote.host = host
    remote.user = user
    remote.password =hostpassword
    remote.allowAnyHosts = true
 
    stage("check ssh"){
         sshCommand remote: remote, command: "ls -lrt"
         sshCommand remote: remote, command: "pwd"
    }

    stage("update system package")
    {
      if(params.UPDATESYSTEM==true){
        sshCommand remote: remote, command: "yum update -y --nobest"
        sshCommand remote: remote, command: "yum install -y python3"
        sshCommand remote: remote, command: "yum install -y git"
        sshCommand remote: remote, command: "yum install -y wget"
        // sshCommand remote: remote, command: "yum install -y ansible-2.13.5"
        sshCommand remote: remote, command: "yum -y install python3-pip"
        sshCommand remote: remote, command: "pip3 -V"
        sshCommand remote: remote, command: "pip3 install --upgrade pip"
        sshCommand remote: remote, command: "pip3 install setuptools_rust"
        sshCommand remote: remote, command: "pip3 install ansible"
        sshCommand remote: remote, command: "mkdir -p /home/centos/mysetup"
        sshCommand remote: remote, command: "ls -la /home/centos/mysetup"
        sshCommand remote: remote, command: "cd /home/centos/mysetup && touch myfile.txt"
        sshCommand remote: remote, command: "touch myfile.txt"
        sshCommand remote: remote, command: "ls -la /home/centos/mysetup"
        sshCommand remote: remote, command: "ansible --version"
        sshCommand remote: remote, command: "python3 --version"
        }
        else{
            echo "skiped system update"
        }    
    }

    stage("Pull templates"){
        sh 'echo $gitusername'
        
        try{
            echo "removing old templates"
            sshCommand remote: remote, command: 'cd /home/centos/mysetup && rm -rf HocalDevops'
        }
        catch (e){
            echo e
        }
        echo "cloning latest"
        sshCommand remote: remote, command: 'cd /home/centos/mysetup && git clone https://'+githocaltoken+'@github.com/hocalwire95/HocalDevops.git'
        sshCommand remote: remote, command: 'ls /home/centos/mysetup'      
    }

    stage("setup mysql"){
        if(params.MYSQL==true){
        sshCommand remote: remote, command: 'cd /home/centos/mysetup && ansible-playbook HocalDevops/mysetup_plays/install_mysql/mysql.yaml  --extra-vars="mysql_new_pw="'+mysql_new_pw  
        }
        else{
            echo "skiped mysql setup"
        }
    }

    stage("setup nginx"){
    if(params.NGINX==true){
            try{
            
            sshCommand remote: remote, command: 'cd /home/centos/mysetup && ansible-playbook HocalDevops/mysetup_plays/install_nginx/nginx.yaml'
            }
            catch(e)
            {
                echo e
            }
        }
          else{
            echo "skiped NGINX setup"
        }
        
    }

    stage("setup nodejs"){
              if(params.NODEJS==true){
                try{
                sshCommand remote: remote, command: 'cd /home/centos/mysetup && ansible-playbook HocalDevops/mysetup_plays/install_nodejs/nodejs1.yaml --extra-vars="githocaltoken="'+githocaltoken
                }
                catch(e){
                    echo e
                }
                try{
                sshCommand remote: remote, command: 'cd /home/centos/mysetup && ansible-playbook HocalDevops/mysetup_plays/install_nodejs/nodejs2.yaml --extra-vars="githocaltoken="'+githocaltoken
                }
                catch(e)
                {
                    echo e
                }
            }
              else{
            echo "skiped NODEJS setup"
        }

    }

    stage("java setup"){
                if(params.JAVA==true){
                    if (javaTar=="yes"){
                        sshCommand remote: remote, command: 'cd /home/centos/mysetup && ansible-playbook HocalDevops/mysetup_plays/install_java/java_alternative.yaml'
                    }
                    else{
                    sshCommand remote: remote, command: 'cd /home/centos/mysetup && ansible-playbook HocalDevops/mysetup_plays/install_java/java.yaml'
                
                    }
                    }
                else{
                    echo "skiped JAVA setup"
                }
            }

    stage("jetty setup"){
            if(params.JETTY==true){
                if (jettyTar=="yes"){
                sshCommand remote: remote, command: 'cd /home/centos/mysetup && ansible-playbook HocalDevops/mysetup_plays/install_jetty/jetty_alternative.yaml --extra-vars="mysql_new_pw="'+mysql_new_pw  
                }
                else {
                sshCommand remote: remote, command: 'cd /home/centos/mysetup && ansible-playbook HocalDevops/mysetup_plays/install_jetty/jetty.yaml'
            
                }
            }
            else{
                   echo "skiped JETTY setup" 
            }    
            }

    stage("image magicmagic"){
            if(params.IMAGEMAGIC==true){
             sshCommand remote: remote, command: 'cd /home/centos/mysetup && ansible-playbook HocalDevops/mysetup_plays/install_imagemagic/imagemagic.yaml'
            }
             else{
                   echo "skiped magicmagic setup" 
            }
        }
}
