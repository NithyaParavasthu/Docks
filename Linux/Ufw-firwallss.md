# UFW

#### This are present in the Ubuntu server by default. But would be in inactive state. Use the follwing command to enable it.
```
ufw enable
```

> Now by default, it will deny all the **incoming** traffic and allow **outgoing**

#### To allow ssh use the following command.
```
ufw allow 22/tcp
```


#### To allow Http use the following command.
```
ufw allow 80/tcp
```


#### To allow Https use the following command.
```
ufw allow 443/tcp
```

#### To allow IPs use the following command.
```
ufw allow from <IP>
```

#### Now to check the allowed ports and deny ports. Use the following command. 
```
ufw status verbose
```

## For verifing it 

#### You can ping with IP as follows
```
ping <IP>
```

#### You can use "nc" command to check it as followes
```
nc -vz <IP> <Port>
```
