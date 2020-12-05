## Github Action
https://medium.com/@yooniks9/github-action-simple-cicd-beginner-93b80e335255

### Preparation
#### 1.connect to your VPS host with SSH (sudo permission is required)
```
ssh ubuntu@254.254.254.254
```

#### 2.create a user github-runner

```
sudo useradd github-runner
```

#### 3.create a ssh key

```
ssh-keygen -t rsa -b 2048 -C github-runner
```

#### 4.create authorization key access for github-runner

- Let’s switch to user github-runner
- Change directory to ~/.ssh
- append github-runner public key to authorized_keys
- Change authorized_keys file permission only user(RW)

```
sudo su github-runner
cd ~/.ssh
cat id_rsa.pub > authorized_keys
chmod 600 authorized_keys
```

#### 5.let’s print out the SSH_PRIVATE_KEY, we will need it for github secrets

```
cat /home/github-runner/.ssh/id_rsa

-----BEGIN RSA PRIVATE KEY-----
MIIEpAIBAAKCAQEAy0aya1Zye2cQ4j9b70hzj8eF+KQl068WjSFxp9Kz/t9az6Mu
zVOL1mZ246c24GttRxL/y8XgW0PBssxFdssa4r4fWiTriDYHnJrLVIzNJymTITws
3Dkb99NtoGUYANKgI18Q+Ql3W25QiABPeJUhNAH3+m7qowg1ZI/g2g0hArktp+QR
0U+VP9h+10p3Juajl/+vqBL3sAqml0JtrC+1KWbf7OAcAhItcOJ0PQ+b4/mFGUxN
N/KrwJijhgzD1C9V16Wg8ZbyvCJi3ctfTi5zbhQ3h6N8UwDahpecAHafxM1KJsF3
3iLqRdJRmlKGFXFS8iHWm3NkjWtRzKLVCVfKwQIDAQABAoIBAQC6Ei7D3k/Q8dkc
oM6wy+ssU4EoxmjxyjWkXlbBPjgRN7bSlhcDUFSSnxx46tlquCtBgfywNKNYJz+p
QR7czLIBl/Lu+xi2iG6TRYaWEITCMmZBZ+nQdF5LjjetOJyuNB7uAdlxfN7KzMHe
nBoi0K+iAaNSlDEnpx3ovWMfdiEfcWzYrTjEBYlXdVpZ3oJG84w5nXjDdWLZmk5p
c5j/UBhnp0od0wUkiPDiQS6OV84Wu7A5D/SrdGptpN2Ici4Sb9TJMHtZKWoMOS+7
uvQuu/zkvCdW8MTqJsQ9P+atmABSs5V6nmvJWKk5YHWMHPp4QIQ6TX6sYxmixrPr
RJ440XBZAoGBAOZnk0DJSFe25Mchi4CvNp7qljvXMHICBNr1/+WREp/3O9YfXiJc
k/GAjv0DwJ35WamntlEJOvWBbFEsUEZZAaTZbZP5Ul5vWKO+iWV8gCTPqEz3PGO2
NlbSxWMNKvFUXhEA8iSLn6T5y/LQOyo2eK0WTSxkh4MWJoczUhTlmwnPAoGBAOHb
n1JT3ejFpfMh2h6mxkn5glRg5CJJz6afzxaxM38Lioav5VL7TRoedMPO6apXhTUm
OSUpDfBk0H3AsTjZvnYSwbbHYKdKlLHYvZongXM0SZJ0grt3/8UJMZlCOw5Zi24J
Zek17764hSIP652icnZgWd8HMi5w/oWUFNJYvVZvAoGAasfT2fGvIl+z9YnZYmFR
uiLUT5VycVP4NhZOa3FppVNqmtjcwqYPNpyzKBWmHlCiulrsxu+1MHSz44bbCU9U
0Lan7y1cfEG0Xeb/PFaP+P94a3sltLyMwH5cVoSSw1erZMwAbOl8qnPOjpDs6qpm
ESnFyG4q05jfcEdboi2rRccCgYEAtd3HON/XfKxKqLDFlpDscny7eX5btVCCKMXe
V4Nw2AhPD41rt5orx7BmjZV7inLXDKkussGQO9ayG9Ch5AlR2HnaCngZWBwyB9Nj
OVI+Zqnf5FJ1Qsep6NsyWRp/u1pXb8+ZhGSuHIEwkvEG5PP9/1wugeyld8f860sG
SSqzq3kCgYA6fyS357OMOTvlWUP42GdiDJCfRjVmRARv38NOUdoO1KBqDSolG+eW
MimBVXAkvxqFh3cw/Oev8426YsWFKTJpvMPi6c+LvWcqKb3THTJ1QtmPxyySuPDU
3Y9PH/RC0vb944ZglRfFWnMYxP+qN2YifxZEBI4zC7MuIB3EICNxQA==
-----END RSA PRIVATE KEY-----
```

#### 6. Environment setup
- exit from github-runner’s workspace
- add github-runner to www-data groups
- create a folder /var/www/github_action
- set ownership : github-runner, group: www-data
- set permission user & groups (read/write/execute)

```
exit
sudo usermod -aG www-data github-runner
sudo mkdir /var/www/github_action
sudo chown github-runner:www-data /var/www/github_action
sudo chmod 775 /var/www/github_action
```

#### Create Secret on Github

- SSH_PORT = 22
- SSH_PRIVATE_KEY = is the key you generate from VPS-Step5
- SSH_SERVER = IP address of your VPS server
- SSH_USER = github_runner
