
# House Price Prediction : Web Application

The housing market is increasing huge, predicting housing prices is not only important to drive real estate efficiency, but also for people looking for properties to buy. However, housing price fluctuations have a lot of influencing factors.

House price prediction model is very essential in filling the information gap and improve real estate efficiency. The aim is to predict price of a property based on various influencing factors like Area type, location, size, total sqft, etc. 

For the prediction method, machine learning algorithms like Linear regression, Lasso regression and decision tree were used. However, Their comparison shows that Linear regression with housing features has the best prediction result. Therefore, Linear regression is been used for this web application.  

## Requirement
- Python 3.7.3
Required libraries:

- numpy 1.16.2
- pandas 0.24.2
- scikit-learn 0.20.3
- Flask 1.0.2
- seaborn 0.9.0
- matplotlib 3.0.3

All the requiements are mentioned in requiements.txt

Along with these, project also needs Postman software to check API, winSCP to transfer project files to AWS EC2 (remote server) and Git bash to access the remote server.

## Dataset
The Bangalore House Price dataset was used to train and evaluate the models. 
It has the house data of Bangalore, Karnataka, which has 13320 records. 

Dataset contains eight separate attributes. Table shows the attribute definitions. 

| Features  | Defination    | 
| :-------- | :------- | 
| Area type (F1) | It tell about the area type, wheather its built-up, plot area or carpet area |
| Availablity (F2) | It tells that wheather its ready to move in or not |
| Location (F3) | It tells the location of the area |
| Size (F4) | It specifies about the area in form of BHK |
| Society (F5) | It tells about the society the area belongs to |
| Total sqft (F6) | It specifies the area in square feet |
| Bath (F7) | Total number of Bathrooms in the property |
| Balcony (F8) | Total number of Balconies in the property |

Among these attributes like Area type, Society, Balcony and Availablity are irrelivant to the prediction model, hence they are been dropped during Data cleaning and additional attribute like "price per sqft" is been added.

[Link to the Dataset](https://www.kaggle.com/datasets/shantanudhakadd/house-prediction-dataset)



## Postman

Postman is an API platform for building and using APIs. Postman simplifies each step of the API lifecycle and streamlines collaboration.

![API](https://github.com/DragnaRR/House-Price-Prediction-Web-Application/assets/95096810/28c50fe0-62c4-4bfb-a568-50a2a892759a)

- It shows that the get and post request on local machine is working fine

![API COL](https://github.com/DragnaRR/House-Price-Prediction-Web-Application/assets/95096810/c2091f84-5f5a-46fc-980f-a8a1dd55d287)

- Getting all the location names for dynamic web page
## Flow chart of the application

![flowchart](https://github.com/DragnaRR/House-Price-Prediction-Web-Application/assets/95096810/25e31ade-2ed1-4b8c-ac4a-2ce4fe599223)
## NGINX

Nginx is a web server that can also be used as a reverse proxy, load balancer, mail proxy and HTTP cache.

By default, NGINX directs to a root index.html file. So, the path to the project's homepage and the name of the file should be mentioned inside the NGINX.conf file.

![nginx config path](https://github.com/DragnaRR/House-Price-Prediction-Web-Application/assets/95096810/03d143f6-c4cb-484d-a722-f115dafe3a8f)

For Example: 
```
location / {
            root   "C:\python\aws\BangloreHomePrices\client";
            index  index.html index.htm app.html;
        }
```
Also reverse proxy is needed to add in nginx.conf file to specify that Flask server will run on port number 5000

```
ocation /api/ {
		rewrite ^/api(.*) $1 break;
		proxy_pass http://127.0.0.1:5000;
	}
```

## winSCP

WinSCP is a free and open-source SSH File Transfer Protocol, File Transfer Protocol, WebDAV, Amazon S3, and secure copy protocol client for Microsoft Windows. Its main function is secure file transfer between a local computer and a remote server.

![winSCP](https://github.com/DragnaRR/House-Price-Prediction-Web-Application/assets/95096810/e8e9e924-7834-44cd-a742-608b0c60b182)

- Host name is the public DNS provided by AWS
- password would be the .pem file provided by AWS during EC2 instance creation

After connecting, move project files to root directory:
/home/ubuntu/BangaloreHomePrices

[Download link to winSCP](https://winscp.net/eng/download.php)
## Deploying web application on AWS EC2 (cloud)

After creating EC2 instance on AWS, copy the public DNS provided by AWS

```
ssh -i "C:\python\aws\BangloreHomePrices\bangalore_home_price.pem" ubuntu@ec2-18-183-159-73.ap-northeast-1.compute.amazonaws.com
```
- ubuntu is the username
- "ec2-18-183-159-73.ap-northeast-1.compute.amazonaws.com" is the public DNS of the instance provided by AWS
- "bangalore_home_price.pem" is the authentication key provided by AWS

then, install NGINX on EC2
```
sudo apt-get update
sudo apt-get install nginx 
```
for checking nginx status
```
sudo service nginx status
```
Go to /etc/nginx/sites-enabled directory and remove the default symlink
```
cd /etc/nginx/sites-enabled
sudo unlink default
```
then, go to sites-available and create a new symlink
```
cd ../sites-available
sudo vim bhp.conf
```
add the contents to bhp.conf file
```
server {
    listen 80;
        server_name bhp;
        root /home/ubuntu/BangloreHomePrices/client;
        index app.html;
        location /api/ {
             rewrite ^/api(.*) $1 break;
             proxy_pass http://127.0.0.1:5000;
        }
}
```
- reverse proxy specifying that flask server will run on port number 5000 and also giving the path to homepage

![BHP](https://github.com/DragnaRR/House-Price-Prediction-Web-Application/assets/95096810/b159531f-8318-4eca-8b75-02372361ab3e)

Then, create symlink for sites-enabled and restart the nginx
```
cd ../sites-enabled
sudo ln -v -s /etc/nginx/sites-available/bhp.conf inside /etc/nginx/sites-enabled
sudo service nginx restart
sudo service nginx status
```
After, setting nginx server, go back to flask server directory and install all the necessary libraries
```
cd ~
cd BangloreHomePrices/server/
sudo apt-get install python3-pip
sudo pip3 install -r requirement.txt
python3 server.py
```

## Screenshot of Web Application

![Project](https://github.com/DragnaRR/House-Price-Prediction-Web-Application/assets/95096810/85667e26-597d-48ca-80a2-6035e647c79b)







## Conclusion
The experimental result shows that among all the algorithms, linear regression gives the highest accuracies ranging from 82.7% to 86% and is considered to be the best model for home price prediction application.

![result](https://github.com/DragnaRR/House-Price-Prediction-Web-Application/assets/95096810/069666e5-6c0d-4223-b7f3-8ba24f518474)
