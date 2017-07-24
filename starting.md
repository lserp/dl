# 1. Launch an EC2 instance to be the Jupyter Notebook Server
- pretty self explanatory, you might want to use the `deep learning ami` for that on top of a GPU powered instance type
- **NOTE**: open up the SG for SSH and the TCP port you want your Jupyter notebook to listen to (more on that later)

# 2. Jupyter Notebook configuration 

## 2.1

- in the EC2 instance run:

```
    pip install jupyter
    openssl req -x509 -nodes -days 365 -newkey rsa:1024 -keyout mykey.key -out mycert.pem 
    jupyter notebook --generate-config
    jupyter notebook password
```
this will 
1. install the jupyter package
2. create a self-signed certificate to use for HTTPS connections to the notebook 
3. generate the configuration file for the jupyter notebook
4. generate a password to use when connecting to the notebook (it's going to be hashed)

## 2.2

Open the `~/.jupyter/jupyter_notebook_config.py` file, uncomment and modify the following parameters as shown:
```
c.NotebookApp.certfile = '/absolute/path/to/your/certificate/mycert.pem'
c.NotebookApp.keyfile = '/absolute/path/to/your/certificate/mykey.key'
c.NotebookApp.ip = '*'
c.NotebookApp.password = '<your hashed password here>'
c.NotebookApp.port = 8888
```

you can choose which port you want your notebook server to listen to, in this case it's 8888. Be sure that matches the SG configuration on step 1


# 3. Connect to the Notebook from your local machine

- create an SSH tunnel between your local machine and the Jupyter notebook server by running: `ssh -i <the private key to connect to the instance> -L 8157:127.0.0.1:8888 ubuntu@<DNS/PublicIP of the Instance>`
- open a browser and point it to `https://127.0.0.1:8157`