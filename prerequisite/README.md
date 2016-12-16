# Installation of swift and openstack client on Windows 7.

##Assumptions:
Below steps only required if student environemnt running on Windows 7.

## Download Cygwin
``https://cygwin.com/install.html``

## Install cygwin along with python and curl.
In Select Packages choose 
       * Python. -> Python Package interpter
       * Net -> curl

## Login to Cygwin

## Download and install Pip
``wget https://bootstrap.pypa.io/get-pip.py --no-check-certificate``

``python get-pip.py``

## Intall python swift-client
``pip install python-swiftclient``


##Install Openstack client (Optional)
``pip install virtualenv``

``virtualenv .venv``

``source .venv/bin/activate``

``pip install --upgrade setuptools``

``pip install python-openstack-client``
