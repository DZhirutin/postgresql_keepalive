Vagrant.configure("2") do |config|
  (1..2).each do |n|
	config.vm.define "node#{n}" do |define|
  	define.ssh.insert_key = false
  	define.vm.box = "ubuntu/bionic64"
  	define.vm.hostname = "node#{n}"
  	define.vm.network :private_network, ip: "192.168.56.1#{n}"
  	# if you would like to use port forwarding, uncomment the line below
  	# define.vm.network :forwarded_port, guest: 5432, host: "543#{n}"
 
  	define.vm.provider :virtualbox do |v|
    	v.cpus = 1
    	v.memory = 1024
    	v.name = "node#{n}"
  	end
 
  	if n == 2
    	define.vm.provision :ansible do |ansible|
      	ansible.limit = "all"
      	ansible.playbook = "playbook.yaml"
		ansible.groups = {
		  "pg_master" => ["node1"],
		  "pg_replica" => ["node2"],
		   # silly group definition
		  "postgresql:children" => ["pg_master", "pg_replica"],
		  "pg_master:vars" => {"repl_mode" => "master"},
		  "pg_replica:vars" => {"repl_mode" => "replica"}
		}

 
      	ansible.host_vars = {
        	"node1" => {:connection_host => "192.168.56.11",
                    	:node_id => 1,
                    	:role => "primary" },
 
        	"node2" => {:connection_host => "192.168.56.12",
                    	:node_id => 2,
  	                    :role => "standby" },
      	}
		
      	# to enable ansible playbook verbose mode, uncomment the line below
      	# ansible.verbose = "v"
    	end
  	end
 
	end
  end
end