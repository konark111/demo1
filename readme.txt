End to end deployment cd/cd pipeline with git --> jenkins --> ansible --> ec2
Have 4 ec2 instances up and running
Ensure passwrod less authentication between jenkins --> ansible & ansible --> webserver( via ssh-keygen & ssh-copy-id user@ip) && set password for users on all servers (passwd username)
steps to be performed on all servers
1. vi /etc/ssh/sshd_config and change 
PermitRootLogin to yes
uncomment AuthorizedKeysFile      .ssh/authorized_keys .ssh/authorized_keys2
set PasswordAuthentication yes
2. install and configure jenkins on jenkins server on ip@8080
3. install and configure ansible on ansible server
4. install and configure apache2(if ubuntu) on web server
5. go to jenkins server install publish over ssh plugin and restart
6. add ssh servers jenkins and ansible in manage jenkins --> system
7. Create jenkins new item and select scm as git and add your repo and enable  GitHub hook trigger for GITScm polling.
7.b) generate api token in jenkins in configure and then in your git repo setting --> webhooks add webhook http/::jenkins server ip/github-webhook/ and add api token from jenkins and save.
8. in Build Steps --> Send files or execute commands over SSH --> ssh server --> select jenkins --> exec command ->rsync -avh /var/lib/jenkins/workspace/demo/* user@ip:/opt/ 
9. in Post-build Actions --> Send build artifacts over SSH --> ssh server --> ansible --> exec command --> ansible-playbook /path to playbook/dep.yml
10 apply and save
11. got to ansible server and add web server ip to hosts/inventory file at /etc/ansible/hosts 
12. check connectivity  bw ansible & webserver by running 'ansible (ip or alias from hosts file) -m ping'. if output ping:pong you are good.
13. write ansible playbook(on path defined in post build actions) 
14. check playbook if you want to be sure by running ansible-playbook dep.yml --check
15. if check is success run your pipeline and you have a fully ci/cd pipeline up and running, make changes to your git repo and commit and jenkins will automatically detect changes and integerate.
Enjoy

