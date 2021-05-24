# manual_kernel_update

Знакомсво с Vagrant и Packer, базовые навыки работы с системами контроля версий (Github). Создание кастомных образов виртуальных машин и их распространение через репозиторий Vagrant Cloud.Обновлению ядра системы из репозитория.

Все ниже описанные действия производятся на компьютере с установленным debian-10.9.0-amd64-netinst

### **Установка ПО**

**Vagrant**

curl -O https://releases.hashicorp.com/vagrant/2.2.6/vagrant_2.2.16_x86_64.deb && \
sudo dpkg -i vagrant_2.2.16_x86_64.deb

**Packer**

curl https://releases.hashicorp.com/packer/1.4.4/packer_1.4.4_linux_amd64.zip | \
sudo gzip -d > /usr/local/bin/packer && \
sudo chmod +x /usr/local/bin/packer

Клонируем репозиторий на рабочую машину. \
git clone https://github.com/Cooler1213/manual_kernel_update.git

Запускаем виртуальную машину \
vagrant up \
Логинимся \
vagrant ssh \
[vagrant@kernel-update ~]$ uname -r \
3.10.0-957.12.2.el7.x86_64

**Обновление ядра** \
Подключаем репозиторий \
sudo yum install -y http://www.elrepo.org/elrepo-release-7.0-3.el7.elrepo.noarch.rpm

Ставим последнее ядро: \
sudo yum --enablerepo elrepo-kernel install kernel-ml -y

Обновляем конфигурацию загрузчика: \
sudo grub2-mkconfig -o /boot/grub2/grub.cfg

Выбираем загрузку с новым ядром по-умолчанию: \
sudo grub2-set-default 0

Перезагружаем виртуальную машину: \
sudo reboot

**Packer** \
Переходим в директорию Pocker \
Создаем образ системы. \
packer build centos.json \
В текущей дириктории появиться файл \
centos-7.7.1908-kernel-5-x86_64-Minimal.box \

**Тестирование созданного образа** \
vagrant box add --name centos-7-5 centos-7.7.1908-kernel-5-x86_64-Minimal.box \
vagrant init centos-7-5 \
произведем замену \
:box_name => "centos-7-5",

vagrant up \
... \
vagrant ssh

Итог проверки \
[vagrant@kernel-update ~]$ uname -r \
5.3.1-1.el7.elrepo.x86_64

vagrant box remove centos-7-5

Полученный бокс опубликован. \
https://app.vagrantup.com/cooler/boxes/centos-7-5
