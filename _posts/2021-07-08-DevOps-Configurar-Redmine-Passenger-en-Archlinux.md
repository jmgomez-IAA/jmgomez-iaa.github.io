---
title: "DevOps Configurar Redmine Passenger en Archlinux"
date: 2021-07-08
categories:
  - blog
tags:
  - redmine
  - sysadmin
  - devops
---

Redmine es un control de

# Descripcion

Instalar con passenger

# Procedimiento


```shell
~]#    85  ls .config/
   86  sudo ls .config/
   87  touch test-project.git/hooks/post-receive
   88  sudo -u git touch test-project.git/hooks/post-receive
   89  sudo -u git nano  test-project.git/hooks/post-receive
   90  sudo -u git chmod +x test-project.git/hooks/post-receive 
   91  cat test-project.git/hooks/post-receive 
   92  ls /var/
   93  ls ../
   94  ls ../http/
   95  mkdir -p /srv/http/jmgomez_iaa_es
   96  ls -la ../http/
   97  sudo -u http mkdir -p /srv/http/jmgomez_iaa_es
   98  sudo mkdir -p /srv/http/jmgomez_iaa_es
   99  sudo git:http chown ../http/jmgomez_iaa_es/
  100  sudo chown  git:http  ../http/jmgomez_iaa_es/
  101  sudo -u git touch /srv/http/jmgomez_iaa_es/index.txt
  102  nano test-project.git/hooks/post-receive 
  103  sudo -u git nano test-project.git/hooks/post-receive 
  104  sudo ls -la /srv/http/jmgomez_iaa_es/
  105  sudo ls -la /srv/http/jmgomez_iaa_es/
  106  sudo -u git nano test-project.git/hooks/post-receive 
  107  sudo -u git nano test-project.git/hooks/post-receive 
  108  sudo ls -la /srv/http/jmgomez_iaa_es/
  109  sudo cat  /srv/http/jmgomez_iaa_es/README.md
  110  sudo -u git nano test-project.git/hooks/post-receive 
  111  cd test-project.git/
  112  ls
  113  cd ..
  114  sudo -u git nano test-project.git/hooks/post-receive 
  115  sudo ls -la /srv/http/jmgomez_iaa_es/
  116  sudo -u git nano test-project.git/hooks/post-receive 
  117  sudo cat  /srv/http/jmgomez_iaa_es/README.md
  118  sudo cat  /srv/http/jmgomez_iaa_es/README.md
  119  sudo -u git nano test-project.git/hooks/post-receive 
  120  sudo cat  /srv/http/jmgomez_iaa_es/README.md
  121  sudo ls -la /srv/http/jmgomez_iaa_es/
  122  sudo -u git jekyll build --source /srv//http/jmgomez_iaa_es/_src --destination /srv/http/jmgomez_iaa_es/_site
  123  sudo -u jmgomez  jekyll build --source /srv//http/jmgomez_iaa_es/_src --destination /srv/http/jmgomez_iaa_es/_site
  124  sudo -u jmgomez  jekyll build --source /srv//http/jmgomez_iaa_es/_src --destination /tmp/jmgomez_iaa_es/_site
  125  sudo -u git nano test-project.git/hooks/post-receive 
  126  sudo -u git nano test-project.git/hooks/post-receive 
  127  sudo -u git ./test-project.git/hooks/post-receive 
  128  sudo -u git nano test-project.git/hooks/post-receive 
  129  sudo -u git ./test-project.git/hooks/post-receive 
  130  ls /tmp/jmgomez_iaa_es/
  131  ls /tmp/jmgomez_iaa_es/_src/
  132  sudo -u git nano test-project.git/hooks/post-receive 
  133  sudo -u git ./test-project.git/hooks/post-receive 
  134  sudo -u git nano test-project.git/hooks/post-receive 
  135  sudo -u git ./test-project.git/hooks/post-receive 
  136  sudo -u git jekyll
  137  cat /etc/passwd | grep git
  138  su git
  139  cat ~/.bashrc
  140  sudo cp ~/.bashrc /srv/git/
  141  sudo cp ~/.bash_profile /srv/git/
  142  ls -la ~/
  143  su git
  144  sudo chwon git:users /srv/git/.bashrc 
  145  sudo chown git:users /srv/git/.bashrc 
  146  sudo chown git:users /srv/git/.bash_profile 
  147  su git
  148  su git
  149  ls /srv/http/jmgomez_iaa_es/
  150  ls /tmp/
  151  ssh www.iaa.es
  152  exit
  153  echo $PATH
  154  exit
  155  pwd
  156  cd /srv/share/Workspace/
  157  ls -la
  158  cd lineas_trabajo/
  159  ls
  160  cd source/
  161  ls
  162  cd jekyll_solutions/
  163  ls -la
  164  cd test-project/
  165  ls -la
  166  nano _src/_config.yml 
  167  history | grep jekyll
  168  jekyll build -s _src -d _site/
  169  history | grep rsync
  170  cat /usr/local/bin/upload_jmgomez_iaa_es 
  171  rsync -vzre ssh --delete _site/* jmgomez@www.iaa.es:public_html
  172  nano _src/_config.yml 
  173  nano  _src/_includes/menu-search.html 
  174  jekyll build -s _src -d _site/
  175  rsync -vzre ssh --delete _site/* jmgomez@www.iaa.es:public_html
  176  nano  _src/_includes/menu-search.html 
  177  jekyll build -s _src -d _site/
  178  rsync -vzre ssh --delete _site/* jmgomez@www.iaa.es:public_html
  179  git commit -am "Update Links of the Menu,with external property to render correctly"
  180  git branch
  181  git checkout development
  182  git merge space-jekyll-template
  183  git checkout master
  184  git merger development
  185  git merge development
  186  git push 
  187  exitg
  188  exit
  189  ls -la
  190  ls /srv/share/Workspace/
  191  ls /srv/share/Workspace/lineas_trabajo/
  192  ls /srv/share/Workspace/lineas_trabajo/source/
  193  ls /srv/share/Workspace/lineas_trabajo/source/script_solutions/
  194  ls /srv/share/Workspace/lineas_trabajo/source/script_solutions/lib_scriptiaa/
  195  ls /srv/share/Workspace/lineas_trabajo/source/script_solutions/lib_scriptiaa/trunk/
  196  cat /srv/share/Workspace/lineas_trabajo/source/script_solutions/lib_scriptiaa/trunk/ssh_upload_pub-id.sh 
  197  visudo
  198  sudo visudo
  199  visudo
  200  sudo visudo
  201  touch /usr/local/bin/upload_jmgomez_iaa_es
  202  sudo visudo
  203  sudo touch /usr/local/bin/upload_jmgomez_iaa_es
  204  sudo chown jmgomez:users /usr/local/bin/upload_jmgomez_iaa_es
  205  ls -la /usr/local/bin/upload_jmgomez_iaa_es 
  206  ls -la /usr/local/bin/
  207  nano /usr/local/bin/upload_jmgomez_iaa_es 
  208  ls /srv/http/jmgomez_iaa_es/
  209  echo "rsync -vzre ssh --delete /srv/http/jmgomez_iaa_es/_site jmgomez@www.iaa.es:public_html/"
  210  echo "rsync -vzre ssh --delete /srv/http/jmgomez_iaa_es/ jmgomez@www.iaa.es:public_html/"
  211  rsync -vzre ssh --delete /srv/http/jmgomez_iaa_es/ jmgomez@www.iaa.es:public_html/
  212  cat  ~/.ssh/id_rsa.pub 
  213  cat  ~/.ssh/id_rsa.pub  | xclip --destination clipboard
  214  cat  ~/.ssh/id_rsa.pub  | ssh jmgomez@iaa.es "cat >> ~/.ssh/authorized_keys"
  215  cat  ~/.ssh/id_rsa.pub  | ssh jmgomez@www.iaa.es "cat >> ~/.ssh/authorized_keys"
  216  rsync -vzre ssh --delete /srv/http/jmgomez_iaa_es/ jmgomez@www.iaa.es:public_html/
  217  nano /usr/local/bin/upload_jmgomez_iaa_es 
  218  nano /usr/local/bin/upload_jmgomez_iaa_es
  219  chmod + x /usr/local/bin/upload_jmgomez_iaa_es 
  220  chmod +x /usr/local/bin/upload_jmgomez_iaa_es 
  221  sudo -u git /srv/git/test-project.git/hooks/post-receive 
  222  sudo -u git nano /srv/git/test-project.git/hooks/post-receive
  223  ls -la /srv/http/jmgomez_iaa_es/
  224  rm -Rf /srv/http/jmgomez_iaa_es/*
  225  sudo rm -Rf /srv/http/jmgomez_iaa_es/*
  226  ls -la /srv/http/jmgomez_iaa_es/
  227  rm /srv/http/jmgomez_iaa_es/.gitignore 
  228  sudo rm /srv/http/jmgomez_iaa_es/.gitignore 
  229  ls -la /srv/http/jmgomez_iaa_es/
  230  sudo  -u git cat /srv/git/test-project.git/hooks/post-receive
  231  sudo -u git nano /srv/git/test-project.git/hooks/post-receive
  232  nano /usr/local/bin/upload_jmgomez_iaa_es
  233  nano /usr/local/bin/upload_jmgomez_iaa_es
  234  sudo -u git nano /srv/git/test-project.git/hooks/post-receive
  235  nano /usr/local/bin/upload_jmgomez_iaa_es
  236  /usr/local/bin/upload_jmgomez_iaa_es 
  237  ls /tmp/jmgomez
  238  ls /tmp/
  239  nano /usr/local/bin/upload_jmgomez_iaa_es
  240  /usr/local/bin/upload_jmgomez_iaa_es 
  241  ls /tmp/jmgomez_iaa_es/
  242  ls /tmp/jmgomez_iaa_es/_src/
  243  nano /usr/local/bin/upload_jmgomez_iaa_es 
  244  sudo -u git nano /srv/git/test-project.git/hooks/post-receive
  245  nano /usr/local/bin/upload_jmgomez_iaa_es 
  246  nano /usr/local/bin/upload_jmgomez_iaa_es 
  247  ls /tmp -la
  248  rm /tmp/jmgomez_iaa_es -rF
  249  rm /tmp/jmgomez_iaa_es -Rf
  250  nano /usr/local/bin/upload_jmgomez_iaa_es 
  251  cat /usr/local/bin/upload_jmgomez_iaa_es 
  252  git --work-tree=/tmp/jmgomez_iaa_web --git-dir=/srv/git/test-project.git/ checkout -f
  253  nano /usr/local/bin/upload_jmgomez_iaa_es 
  254  sudo -u git nano /srv/git/test-project.git/hooks/post-receive
  255  ls /tmp/jmgomez_iaa_es/ -la
  256  /usr/local/bin/upload_jmgomez_iaa_es 
  257  sudo -u git chown jmgomez:users /tmp/jmgomez_iaa_es/ -R
  258  ls /tmp/jmgomez_iaa_es/ -la
  259  sudo chown jmgomez:users /tmp/jmgomez_iaa_es/ -
  260  ls /tmp/jmgomez_iaa_es/ -la
  261  /usr/local/bin/upload_jmgomez_iaa_es 
  262  ls /tmp/jmgomez_iaa_es/_site/ -la
  263  ls /tmp/jmgomez_iaa_es/_site/Informatica_Forense_Recover_HDD/ -la
  264  cat /usr/local/bin/upload_jmgomez_iaa_es 
  265  rsync -vzre ssh --delete /tmp/jmgomez_iaa_es/_site/* jmgomez@www.iaa.es:public_html/
  266  ssh www.iaa.es
  267  rm -Rf /tmp/jmgomez_iaa_es -R
  268  cat /usr/local/bin/upload_jmgomez_iaa_es 
  269  nano /usr/local/bin/upload_jmgomez_iaa_es 
  270  git --work-tree=/tmp/jmgomez_iaa_web --git-dir=/srv/git/test-project.git/ checkout -f
  271  nano /usr/local/bin/upload_jmgomez_iaa_es 
  272  git --work-tree=/tmp/jmgomez_iaa_web --git-dir=/srv/git/test-project.git/.git  checkout -f
  273  git --work-tree=/tmp/jmgomez_iaa_web --git-dir=/srv/git/test-project.git/  checkout -f
  274  mkdir -p /tmp/jmgomez_iaa_web
  275  git --work-tree=/tmp/jmgomez_iaa_web --git-dir=/srv/git/test-project.git/  checkout -f
  276  sudo -u git git --work-tree=/tmp/jmgomez_iaa_web --git-dir=/srv/git/test-project.git/  checkout -f
  277  mkdir /tmp/jmgomez_iaa_web
  278  rmdir /tmp/jmgomez_iaa_web/
  279  rmdir /tmp/jmgomez_iaa_web
  280  sudo -u git mkdir /tmp/jmgomez_iaa_web
  281  sudo -u git git --work-tree=/tmp/jmgomez_iaa_web --git-dir=/srv/git/test-project.git/  checkout -f
  282  /usr/local/bin/upload_jmgomez_iaa_es 
  283  sudo rmdir /tmp/jmgomez_iaa_web
  284  sudo -u git chown jmgomez:users /tmp/jmgomez_iaa_web/
  285  sudo -u git chmod 777 /tmp/jmgomez_iaa_web/
  286  sudo -u git chmod 777 /tmp/jmgomez_iaa_web/ -R
  287  jekyll build --source /tmp/jmgomez_iaa_web/_src/ --destination /tmp/jmgomez_iaa_web/_site
  288  sudo -u git chmod 777 /tmp/j git nano
  289  nano /usr/local/bin/upload_jmgomez_iaa_es 
  290  sudo git -u nano /srv/git/test-project.git/hooks/post-receive 
  291  sudo -u git nano /srv/git/test-project.git/hooks/post-receive 
  292  rm -rF /tmp/jmgomez_iaa_web 
  293  rm -Rf /tmp/jmgomez_iaa_web 
  294  sudo rm -Rf /tmp/jmgomez_iaa_web 
  295  nano /usr/local/bin/upload_jmgomez_iaa_es 
  296  sudo -u git nano /srv/git/test-project.git/hooks/post-receive 
  297  shell
  298  $env
  299  echo $SHELL
  300  su git
  301  sudo -u jmgomez echo $SHELL
  302  nano /usr/local/bin/upload_jmgomez_iaa_es 
  303  bash
  304  nano /usr/local/bin/upload_jmgomez_iaa_es 
  305  ls /tmp/jmgomez_iaa_es/
  306  ls /tmp/jmgomez_iaa_es/ -la
  307  sudo rm -Rf /tmp/jmgomez_iaa_es 
  308  ls /tmp/jmgomez_iaa_es/ -la
  309  ls /tmp/jmgomez_iaa_es/_site -la
  310  nano /usr/local/bin/upload_jmgomez_iaa_es 
  311  sudo rm Rf /tmp/jmgomez_iaa_es
  312  sudo rm -Rf /tmp/jmgomez_iaa_es
  313  sudo -u git nano /srv/git/test-project.git/hooks/post-receive 
  314  sudo -u git rm -Rf /tmp/jmgomez_iaa_es
  315  sudo -u jmgomez  rm -Rf /tmp/jmgomez_iaa_es
  316  sudo -u git rm -Rf /tmp/jmgomez_iaa_es
  317  nano /usr/local/bin/upload_jmgomez_iaa_es 
  318  sudo -u git cat /srv/git/test-project.git/hooks/post-receive 
  319  nano /usr/local/bin/upload_jmgomez_iaa_es 
  320  rm /tmp/jmgomez_iaa_es/_src/.jekyll-cache -Rf
  321  sudo rmdir /tmp/jmgomez_iaa_es
  322  sudo rm -Rf /tmp/jmgomez_iaa_es
  323  cd /srv/share/Workspace/
  324  ls -la
  325  cd lineas_trabajo/
  326  ls
  327  cd source/
  328  ls
  329  cd jekyll_solutions/
  330  ls -la
  331  git clone git@udit76.iaa.es:/srv/git/test-project.git
  332  history | grep .pub
  333  cat ~/.ssh/id_rsa.pub | sudo -u git "cat >> /srv/git/.ssh/authorized_keys"
  334  cat ~/.ssh/id_rsa.pub | sudo -u git cat >> /srv/git/.ssh/authorized_keys
  335  ls -la
  336  cd test-project/
  337  ls -la
  338  jekyll build -s _src -d _site
  339  ls _site/
  340  git checkout development
  341  jekyll build -s _src -d _site
  342  ls -la _site
  343  cd ..
  344  rm test-project -Rf
  345  nano /usr/local/bin/upload_jmgomez_iaa_es 
  346  sudo rm /tmp/jmgomez_iaa_es -Rf
  347  ssh www.iaa.es
  348  nano /usr/local/bin/upload_jmgomez_iaa_es 
  349  git clone git@udit76.iaa.es:/srv/git/test-project.git
  350  git banch
  351  git branch
  352  cd test-project/
  353  git branch
  354  git branch -r
  355  git checkout space-jekyll-template
  356  git pull
  357  cat _src/_config.yml 
  358  ls -la
  359  jekyll build -s _src -d _site
  360  cat /usr/local/bin/upload_jmgomez_iaa_es 
  361  rsync -vzre ssh --delete _site/* jmgomez@www.iaa.es:public_html
  362  rsync -vuv ssh --delete _site/* jmgomez@www.iaa.es:public_html
  363  rsync -avh ssh --delete _site/* jmgomez@www.iaa.es:public_html
  364  ssh www.iaa.es
  365  rsync -vzre ssh --delete _site/* jmgomez@www.iaa.es:public_html
  366  nano _src/_includes/menu-search.html 
  367  jekyll build -s _src -d _site
  368  rsync -vzre ssh --delete _site/* jmgomez@www.iaa.es:public_html
  369  nano _src/_includes/menu-search.html 
  370  nano _src/_config.yml 
  371  jekyll build -s _src -d _site
  372  rsync -vzre ssh --delete _site/* jmgomez@www.iaa.es:public_html
  373  git commit -am "Issue with external links on the Menu. External flag should be included in _config.yml for external links."
  374  git config --global user.email jmgomez
  375  git config --global user.name Juanma
  376  git commit -am "Issue with external links on the Menu. External flag should be included in _config.yml for external links."
  377  git push 
  378  git checkout development
  379  git merge space-jekyll-template
  380  git checkout master
  381  git merge development
  382  git push --all
  383  git checkout space-jekyll-template
  384  nano _src/_config.yml 
  385  nano _src/_includes/menu-search.html
  386  jekyll build -s _src -d _site
  387  rsync -vzre ssh --delete _site/* jmgomez@www.iaa.es:public_html
  388  git commit -am "Issue with kwy-trigger on external links on the Menu. Add the generation to external links if block."
  389  git checkout development
  390  git merge space-jekyll-template
  391  git checkout master
  392  git merge development
  393  git push --all
  394  cat _src/_config.yml 
  395  nano _src/_config.yml 
  396  git branch
  397  git checkout space-jekyll-template
  398  nano _src/_config.yml 
  399  jekyll build -s _src -d _site
  400  rsync -vzre ssh --delete _site/* jmgomez@www.iaa.es:public_html
  401  jekyll build -s _src -d _site
  402  rsync -vzre ssh --delete _site/* jmgomez@www.iaa.es:public_html
  403  nano _src/_includes/menu-search.html
  404  jekyll build -s _src -d _site
  405  rsync -vzre ssh --delete _site/* jmgomez@www.iaa.es:public_html
  406  nano _src/_includes/menu-search.html
  407  jekyll build -s _src -d _site
  408  rsync -vzre ssh --delete _site/* jmgomez@www.iaa.es:public_html
  409  nano _src/_includes/menu-search.html
  410  ls -la
  411  systemctl --list-units
  412  systemctl --list-unit
  413  systemctl --help
  414  systemctl list-units
  415  ddh 10.9.0.104
  416  ssh pi@10.9.0.104
  417  exit
  418  sudo pacman -S jruby
  419  sudo pacman -Syu
  420  pwd
  421  cd /srv/share/Workspace/
  422  ls -la
  423  cd lineas_trabajo/
  424  ls -l
  425  cd source/
  426  ls
  427  mkdir redmine_solutions
  428  cd redmine_solutions/
  429  git clone https://github.com/redmine/redmine
  430  cd redmine/
  431  git remote rename origin upstream
  432  git remote add origin git@udit76.iaa.es:redmine.git
  433  git -r branch
  434  git  branch
  435  git  branch -a
  436  git checkout -b redmine/4.2-stable upstream/3.2-stable
  437  git branch
  438  cd ..
  439  rm redmine -R
  440  sudo rm redmine -R
  441  git clone https://github.com/redmine/redmine
  442  cd redmine/
  443  git remote rename origin upstream
  444  git remote add origin git@udit76.iaa.es:redmine.git
  445  git checkout -b redmine/3.2-stable upstream/3.2-stable
  446  git checkout -b local/3.2-stable
  447  git push --set-upstream origin local/3.2-stable
  448  git push --set-upstream origin local/3.2-stable
  449  sudo -u git
  450  su git
  451  su git
  452  git push --set-upstream origin local/3.2-stable
  453  ls -la
  454  pwd
  455  cd /srv/
  456  ls
  457  cd git
  458  ls
  459  ls -la
  460  sudo -u git git init --bare redmine.git
  461  cd ../share/Workspace/lineas_trabajo/source/redmine_solutions/
  462  git push --set-upstream origin local/3.2-stable
  463  cd redmine/
  464  git push --set-upstream origin local/3.2-stable
  465  ls -la
  466  java -V
  467  java -v
  468  java --version
  469  java --help
  470  java
  471  java -version
  472  jruby -v
  473  pwd
  474  ls -la
  475  clear
  476  ls
  477  cp config/database.yml.example  config/database.yml
  478  nano config/database.yml
  479  gem install bundler
  480  echo $PATH
  481  gem -v
  482  nano ~/.bash_profile 
  483  exit
  484  cd /srv/share/Workspace/lineas_trabajo/source/redmine_solutions/
  485  ls -la
  486  cd redmine/
  487  echo $PATH
  488  bundle install
  489  nano Gemfile 
  490  ls
  491  nano Rakefile 
  492  gem list bundler
  493  gem install bundler:2.0.0.pre.3
  494  bundle _2.0.0.pre.3_install
  495  bundle _2.0.0.pre.3_ install
  496  bundle exec rake db:migrate
  497  nano Gemfile
  498  exit
  499  links udit76.iaa.es:8080/host-manager/html
  500  links udit76.iaa.es:8080/host-manager/html
  501  links udit76.iaa.es:8080/host-manager/html
  502  links udit76.iaa.es:8080/manager/html
  503  links udit76.iaa.es:8080/manager/html
  504  links udit76.iaa.es:8080/manager/html
  505  ss
  506  links udit76.iaa.es:8080/manager/html
  507  cd Workspace/
  508  ls
  509  cd /srv/share/Workspace/lineas_trabajo/
  510  ls
  511  cd source/
  512  ls
  513  cd redmine_solutions/
  514  ls -la
  515  cd redmine/
  516  ls
  517  git status
  518  git branch
  519  pacman -S postgresql
  520  sudo pacman -S postgresql
  521  ls /var/lib/postgres/data/
  522  su -u postgres ls /var/lib/postgres/data/
  523  sudo ls /var/lib/postgres/data/
  524  sudo rm  /var/lib/postgres/data -R
  525  sudo -iu postgres
  526  sudo ls /var/lib/postgres/ -la
  527  sudo mkdir -p /var/lib/postgres/data
  528  sudo chown postgres:postgres /var/lib/postgres/data/
  529  sudo -iu postgres
  530  pwd
  531  git branch
  532  git branch -r
  533  git checkout -b redmine/4.2-stable upstream/4.2-stable
  534  git checkout -b local/4.2-stable
  535  nano config/database.yml
  536  bundle install --without development test
  537  bundle exec rake generate_secret_token
  538  gem install bundler
  539  bundle install --without development test
  540  bundle exec rake generate_secret_token
  541  RAILS_ENV=production bundle exec rake db:migrate
  542  mkdir -p tmp tmp/pdf public/plugin_assets
  543  sudo chown -R redmine:redmine files log tmp public/plugin_assets
  544  cd ..
  545  sudo pacman -S install redmine
  546  sudo pacman -S redmine
  547  /opt/ruby2.6/bin/bundle-2.6 exec rake generate_secret_token
  548  cd /usr/share/webapps/redmine/
  549  /opt/ruby2.6/bin/bundle-2.6 exec rake generate_secret_token
  550  cp config/database.yml.example config/database.yml
  551  ls -la
  552  sudo cp config/database.yml.example config/database.yml
  553  sudo nano config/database.yml
  554  /opt/ruby2.6/bin/bundle-2.6 exec rake generate_secret_token
  555  sudo chown http:http db 
  556  sudo -u http -g http RAILS_ENV=production /opt/ruby2.6/bin/bundle-2.6 exec rake db:migrate
  557  /opt/ruby2.6/bin/bundle-2.6 exec rake generate_secret_token
  558  sudo /opt/ruby2.6/bin/bundle-2.6 exec rake generate_secret_token
  559  sudo -u http -g http RAILS_ENV=production /opt/ruby2.6/bin/bundle-2.6 exec rake db:migrate
  560  sudo -u http -g http RAILS_ENV=production /opt/ruby2.6/bin/bundle-2.6 exec rake redmine:load_default_data
  561  sudo -u http -g http RAILS_ENV=production REDMINE_LANG=en /opt/ruby2.6/bin/bundle-2.6 exec rake redmine:load_default_data
  562  ls -la files
  563  ls -la /var/lib/redmine/files/
  564  sudo -u http -g http /opt/ruby2.6/bin/ruby-2.6 bin/rails server webrick -e production
  565  /opt/ruby2.6/bin/gem-2.6 install passanger
  566  /opt/ruby2.6/bin/gem-2.6 pristine ffi --version 1.15.0
  567  /opt/ruby2.6/bin/gem-2.6 pristine json --version 1.8.6 mimemagin --version 0.4.3
  568  /opt/ruby2.6/bin/gem-2.6 pristine json --version 1.8.6
  569  /opt/ruby2.6/bin/gem-2.6 pristine  mimemagic --version 0.4.3
  570  /opt/ruby2.6/bin/gem-2.6 pristine  mysql2 --version 0.4.10
  571  /opt/ruby2.6/bin/gem-2.6 pristine  nokogiri --version 1.7.2
  572  /opt/ruby2.6/bin/gem-2.6 pristine redcarpet --version 3.3.4
  573  /opt/ruby2.6/bin/gem-2.6 pristine rmagick --version 4.2.2
  574  /opt/ruby2.6/bin/gem-2.6 install passanger
  575  /opt/ruby2.6/bin/gem-2.6 install passenger
  576  /opt/ruby2.6/bin/gem-2.6 env
  577  cd /home/jmgomez/.gem/ruby/2.6.0/bin/
  578  ls
  579  ./passenger-install-apache2-module 
  580  echo $PATH
  581  PATH=/home/jmgomez/.gem/ruby/2.6.0/gems/passenger-6.0.8/bin:${PATH}
  582  passenger-config about ruby-command
  583  pwd
  584  history > /srv/share/Workspace/lineas_trabajo/source/redmine_solutions/install_redmine4_1.txt

```




# Conclusiones
Ahora debemos añadir el servidor svn a nuestra politica de backups, pero estamos listos para trabajar con el.

# Referencias

[]()