box      = 'ubuntu-13.1'
#url      = 'http://files.vagrantup.com/precise64.box'
hostname = 'phpdevbox-gui'
domain   = 'local'
ip       = '192.168.56.105'
ram      = '2046'
























Vagrant::Config.run do |config|
  config.vm.provider = virtualbox
  config.vm.box = box
  config.vm.box_url = url
  config.vm.host_name = hostname + '.' + domain
  config.vm.network :hostonly, ip, :adapter => 2
  config.vm.gui = true
  
  // windows host, nfs -> false
  config.vm.share_folder("v-root", "/vagrant", ".", :nfs => true)
  
  config.vm.customize [
    'modifyvm', :id,
    '--name', hostname,
    '--memory', ram
  ]

  config.vm.provision :puppet do |puppet|
    puppet.manifests_path = "puppet/vagrant/manifests"
    puppet.manifest_file = "app.pp"
  end

end



require "yaml"

CONF = YAML.load(File.open(File.join(File.dirname(__FILE__), "VagrantConfig.yaml"), File::RDONLY).read)
MOUNT_POINT  = "/vagrant/" + CONF['project']['dir'] 

Vagrant::Config.run do |config|

  config.vm.box     = "precise64"
  config.vm.box_url = "http://files.vagrantup.com/precise64.box"

  config.vm.network :hostonly, CONF['ip'], :adapter => 2
  config.vm.host_name = CONF['hostname'] + '.' + CONF['domain']

  // windows host, nfs -> false
  // config.vm.share_folder("v-root", "/vagrant", ".", :nfs => true)
  config.vm.share_folder CONF['project']['dir'], MOUNT_POINT, CONF['project']['dir']
  config.vm.share_folder "PuppetFiles", "/etc/puppet/files", "puppet/files"

  config.vm.provision :puppet, :options => ["--fileserverconfig=/vagrant/fileserver.conf"] do |puppet|
  
    puppet.manifests_path = "puppet/manifests"
    puppet.manifest_file  = "app.pp"
    puppet.module_path    = "puppet/manifests/modules"
    puppet.facter = [
            ['project_dir', MOUNT_POINT],
            ['project_url', CONF['hostname'] + '.' + CONF['domain']],
            ['db_username', CONF['db']['username']],
            ['db_password', CONF['db']['password']],
            ['db_name', CONF['db']['name']],
            ['db_root_password', CONF['db']['root_password']],
    ]
  end

end