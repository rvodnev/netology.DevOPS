# Курсовая работа по итогам модуля "DevOps и системное администрирование"

Курсовая работа необходима для проверки практических навыков, полученных в ходе прохождения курса "DevOps и системное администрирование".

Мы создадим и настроим виртуальное рабочее место. Позже вы сможете использовать эту систему для выполнения домашних заданий по курсу

## Задание

>1. Создайте виртуальную машину Linux.

```bash
PS C:\vagrant> vagrant up
Bringing machine 'default' up with 'virtualbox' provider...
==> default: Importing base box 'bento/ubuntu-20.04'...
==> default: Matching MAC address for NAT networking...
==> default: Checking if box 'bento/ubuntu-20.04' version '202107.28.0' is up to date...
==> default: A newer version of the box 'bento/ubuntu-20.04' for provider 'virtualbox' is
==> default: available! You currently have version '202107.28.0'. The latest is version
==> default: '202112.19.0'. Run `vagrant box update` to update.
==> default: Setting the name of the VM: vagrant_default_1640102936082_80374
==> default: Clearing any previously set network interfaces...
==> default: Preparing network interfaces based on configuration...
    default: Adapter 1: nat
==> default: Forwarding ports...
    default: 19999 (guest) => 19999 (host) (adapter 1)
    default: 22 (guest) => 2222 (host) (adapter 1)
==> default: Running 'pre-boot' VM customizations...
==> default: Booting VM...
==> default: Waiting for machine to boot. This may take a few minutes...
    default: SSH address: 127.0.0.1:2222
    default: SSH username: vagrant
    default: SSH auth method: private key
    default:
    default: Vagrant insecure key detected. Vagrant will automatically replace
    default: this with a newly generated keypair for better security.
    default:
    default: Inserting generated public key within guest...
    default: Removing insecure key from the guest if it's present...
    default: Key inserted! Disconnecting and reconnecting using new SSH key...
==> default: Machine booted and ready!
==> default: Checking for guest additions in VM...
==> default: Mounting shared folders...
    default: /vagrant => C:/vagrant
```

>2. Установите ufw и разрешите к этой машине сессии на порты 22 и 443, при этом трафик на интерфейсе localhost (lo) должен ходить свободно на все порты.

Восстановим правила UFW по умолчанию, для восстановления настроек по умолчанию для UFW необходимо использовать следующие команды:
```bash
sudo ufw default deny incoming
sudo ufw default allow outgoing
```
Чтобы разрешить на сервере входящие соединения SSH и https, нужно использовать следующую команду:
```bash
vagrant@vagrant:~$ sudo ufw allow ssh
Rules updated
Rules updated (v6)

vagrant@vagrant:~$ sudo ufw allow https
Rules updated
Rules updated (v6)
```
Эта команда создаст правила брандмауэра, которые разрешат все соединения на порту 22 и 443. UFW знает, какой порт имеет в виду команда allow ssh, потому что он указан как услуга в файле /etc/services

Активируем ufw:

```bash
vagrant@vagrant:~$ sudo ufw enable
Command may disrupt existing ssh connections. Proceed with operation (y|n)? yes
Firewall is active and enabled on system startup
```
Проверяем правила:
```bash
vagrant@vagrant:~$ sudo ufw status numbered
Status: active

     To                         Action      From
     --                         ------      ----
[ 1] 22/tcp                     ALLOW IN    Anywhere
[ 2] 443/tcp                    ALLOW IN    Anywhere
[ 3] 22/tcp (v6)                ALLOW IN    Anywhere (v6)
[ 4] 443/tcp (v6)               ALLOW IN    Anywhere (v6)

vagrant@vagrant:~$
```

>3. Установите hashicorp vault ([инструкция по ссылке](https://learn.hashicorp.com/tutorials/vault/getting-started-install?in=vault/getting-started#install-vault)).


Установил и проверил вывод "Usage":

```bash
vagrant@vagrant:~$ curl -fsSL https://apt.releases.hashicorp.com/gpg | sudo apt-key add -
OK
vagrant@vagrant:~$ sudo apt-add-repository "deb [arch=amd64] https://apt.releases.hashicorp.com $(lsb_release -cs) main"
Hit:1 http://archive.ubuntu.com/ubuntu focal InRelease
Get:2 https://apt.releases.hashicorp.com focal InRelease [9,495 B]
Get:3 http://archive.ubuntu.com/ubuntu focal-updates InRelease [114 kB]
Get:4 http://security.ubuntu.com/ubuntu focal-security InRelease [114 kB]
Get:5 http://archive.ubuntu.com/ubuntu focal-backports InRelease [108 kB]
Get:6 https://apt.releases.hashicorp.com focal/main amd64 Packages [40.5 kB]
Get:7 http://archive.ubuntu.com/ubuntu focal-updates/main amd64 Packages [1,400 kB]
Get:8 http://security.ubuntu.com/ubuntu focal-security/main amd64 Packages [1,069 kB]
Get:9 http://archive.ubuntu.com/ubuntu focal-updates/main i386 Packages [574 kB]
Get:10 http://archive.ubuntu.com/ubuntu focal-updates/main Translation-en [283 kB]
Get:11 http://security.ubuntu.com/ubuntu focal-security/main i386 Packages [345 kB]
Get:12 http://archive.ubuntu.com/ubuntu focal-updates/restricted amd64 Packages [616 kB]
Get:13 http://security.ubuntu.com/ubuntu focal-security/main Translation-en [197 kB]
Get:14 http://archive.ubuntu.com/ubuntu focal-updates/restricted i386 Packages [21.8 kB]
Get:15 http://archive.ubuntu.com/ubuntu focal-updates/restricted Translation-en [88.1 kB]
Get:16 http://archive.ubuntu.com/ubuntu focal-updates/universe i386 Packages [654 kB]
Get:17 http://security.ubuntu.com/ubuntu focal-security/restricted amd64 Packages [566 kB]
Get:18 http://archive.ubuntu.com/ubuntu focal-updates/universe amd64 Packages [884 kB]
Get:19 http://security.ubuntu.com/ubuntu focal-security/restricted i386 Packages [20.5 kB]
Get:20 http://security.ubuntu.com/ubuntu focal-security/restricted Translation-en [80.9 kB]
Get:21 http://security.ubuntu.com/ubuntu focal-security/universe amd64 Packages [668 kB]
Get:22 http://archive.ubuntu.com/ubuntu focal-updates/universe Translation-en [193 kB]
Get:23 http://archive.ubuntu.com/ubuntu focal-updates/multiverse amd64 Packages [24.8 kB]
Get:24 http://archive.ubuntu.com/ubuntu focal-updates/multiverse i386 Packages [8,456 B]
Get:25 http://archive.ubuntu.com/ubuntu focal-updates/multiverse Translation-en [6,928 B]
Get:26 http://archive.ubuntu.com/ubuntu focal-backports/main i386 Packages [34.5 kB]
Get:27 http://archive.ubuntu.com/ubuntu focal-backports/main amd64 Packages [42.0 kB]
Get:28 http://archive.ubuntu.com/ubuntu focal-backports/main Translation-en [10.0 kB]
Get:29 http://archive.ubuntu.com/ubuntu focal-backports/universe amd64 Packages [18.9 kB]
Get:30 http://archive.ubuntu.com/ubuntu focal-backports/universe i386 Packages [10.5 kB]
Get:31 http://archive.ubuntu.com/ubuntu focal-backports/universe Translation-en [7,492 B]
Get:32 http://security.ubuntu.com/ubuntu focal-security/universe i386 Packages [525 kB]
Get:33 http://security.ubuntu.com/ubuntu focal-security/universe Translation-en [112 kB]
Get:34 http://security.ubuntu.com/ubuntu focal-security/multiverse i386 Packages [7,212 B]
Get:35 http://security.ubuntu.com/ubuntu focal-security/multiverse amd64 Packages [21.9 kB]
Get:36 http://security.ubuntu.com/ubuntu focal-security/multiverse Translation-en [4,948 B]
Fetched 8,880 kB in 11s (774 kB/s)
Reading package lists... Done
vagrant@vagrant:~$ sudo apt-get update && sudo apt-get install vault
Hit:1 http://archive.ubuntu.com/ubuntu focal InRelease
Hit:2 http://archive.ubuntu.com/ubuntu focal-updates InRelease
Hit:3 http://security.ubuntu.com/ubuntu focal-security InRelease
Hit:4 http://archive.ubuntu.com/ubuntu focal-backports InRelease
Hit:5 https://apt.releases.hashicorp.com focal InRelease
Reading package lists... Done
Reading package lists... Done
Building dependency tree
Reading state information... Done
The following NEW packages will be installed:
  vault
0 upgraded, 1 newly installed, 0 to remove and 108 not upgraded.
Need to get 69.4 MB of archives.
After this operation, 188 MB of additional disk space will be used.
Get:1 https://apt.releases.hashicorp.com focal/main amd64 vault amd64 1.9.1 [69.4 MB]
Fetched 69.4 MB in 10s (6,835 kB/s)
Selecting previously unselected package vault.
(Reading database ... 41552 files and directories currently installed.)
Preparing to unpack .../archives/vault_1.9.1_amd64.deb ...
Unpacking vault (1.9.1) ...
Setting up vault (1.9.1) ...
Generating Vault TLS key and self-signed certificate...
Generating a RSA private key
....................................................................++++
................++++
writing new private key to 'tls.key'
-----
Vault TLS key and self-signed certificate have been generated in '/opt/vault/tls'.


vagrant@vagrant:~$ vault
Usage: vault <command> [args]

Common commands:
    read        Read data and retrieves secrets
    write       Write data, configuration, and secrets
    delete      Delete secrets and configuration
    list        List data or secrets
    login       Authenticate locally
    agent       Start a Vault agent
    server      Start a Vault server
    status      Print seal and HA status
    unwrap      Unwrap a wrapped secret

Other commands:
    audit          Interact with audit devices
    auth           Interact with auth methods
    debug          Runs the debug command
    kv             Interact with Vault's Key-Value storage
    lease          Interact with leases
    monitor        Stream log messages from a Vault server
    namespace      Interact with namespaces
    operator       Perform operator-specific tasks
    path-help      Retrieve API help for paths
    plugin         Interact with Vault plugins and catalog
    policy         Interact with policies
    print          Prints runtime configurations
    secrets        Interact with secrets engines
    ssh            Initiate an SSH session
    token          Interact with tokens
vagrant@vagrant:~$
```

>4. Cоздайте центр сертификации по инструкции ([ссылка](https://learn.hashicorp.com/tutorials/vault/pki-engine?in=vault/secrets-management)) и выпустите сертификат для использования его в настройке веб-сервера nginx (срок жизни сертификата - месяц).

Установите `jq` в своей среде Vault, чтобы следовать примерам, в которых используется этот инструмент:
```bash
vagrant@vagrant:~$ sudo apt-get install jq
Reading package lists... Done
Building dependency tree
Reading state information... Done
The following additional packages will be installed:
  libjq1 libonig5
The following NEW packages will be installed:
  jq libjq1 libonig5
0 upgraded, 3 newly installed, 0 to remove and 108 not upgraded.
Need to get 313 kB of archives.
After this operation, 1,062 kB of additional disk space will be used.
Do you want to continue? [Y/n] yes
Get:1 http://archive.ubuntu.com/ubuntu focal/universe amd64 libonig5 amd64 6.9.4-1 [142 kB]
Get:2 http://archive.ubuntu.com/ubuntu focal-updates/universe amd64 libjq1 amd64 1.6-1ubuntu0.20.04.1 [121 kB]
Get:3 http://archive.ubuntu.com/ubuntu focal-updates/universe amd64 jq amd64 1.6-1ubuntu0.20.04.1 [50.2 kB]
Fetched 313 kB in 1s (352 kB/s)
Selecting previously unselected package libonig5:amd64.
(Reading database ... 41558 files and directories currently installed.)
Preparing to unpack .../libonig5_6.9.4-1_amd64.deb ...
Unpacking libonig5:amd64 (6.9.4-1) ...
Selecting previously unselected package libjq1:amd64.
Preparing to unpack .../libjq1_1.6-1ubuntu0.20.04.1_amd64.deb ...
Unpacking libjq1:amd64 (1.6-1ubuntu0.20.04.1) ...
Selecting previously unselected package jq.
Preparing to unpack .../jq_1.6-1ubuntu0.20.04.1_amd64.deb ...
Unpacking jq (1.6-1ubuntu0.20.04.1) ...
Setting up libonig5:amd64 (6.9.4-1) ...
Setting up libjq1:amd64 (1.6-1ubuntu0.20.04.1) ...
Setting up jq (1.6-1ubuntu0.20.04.1) ...
Processing triggers for man-db (2.9.1-1) ...
Processing triggers for libc-bin (2.31-0ubuntu9.2) ...
vagrant@vagrant:~$
```
Создали [политики](https://learn.hashicorp.com/tutorials/vault/policies)


В отдельной сессии:
```bash
vault server -dev -dev-root-token-id=root
```
В основной сессии:
```bash
vagrant@vagrant:~$ export VAULT_ADDR=http://127.0.0.1:8200
vagrant@vagrant:~$ export VAULT_TOKEN=root
vagrant@vagrant:~$ vault policy list
default
root
vagrant@vagrant:~$ vault policy write admin admin-policy.hcl
Success! Uploaded policy: admin
vagrant@vagrant:~$ vault policy list
admin
default
root
vagrant@vagrant:~$ ADMIN_TOKEN=$(vault token create -format=json -policy="admin" | jq -r ".auth.client_token")
vagrant@vagrant:~$ echo $ADMIN_TOKEN
s.yS3ylIPjCzhRSScFQZN4sUym
vagrant@vagrant:~$ vault token capabilities $ADMIN_TOKEN sys/auth/approle
create, delete, sudo, update
vagrant@vagrant:~$ vault token capabilities $ADMIN_TOKEN identity/entity
deny
vagrant@vagrant:~$

```
Запустить Vault

```bash

vagrant@vagrant:~$ export VAULT_ADDR=http://127.0.0.1:8200  # Экспортируйте переменную среды для vault интерфейса командной строки для адресации сервера Vault.
vagrant@vagrant:~$ export VAULT_TOKEN=root  # Экспортируйте переменную среды для vault CLI для аутентификации на сервере Vault.
```

Шаг 1. Создайте корневой центр сертификации.

```bash
vagrant@vagrant:~$ vault secrets enable pki  #Включите pki механизм секретов на pki пути.
Success! Enabled the pki secrets engine at: pki/  
vagrant@vagrant:~$ vault secrets tune -max-lease-ttl=87600h pki   # Настройте pki механизм секретов для выдачи сертификатов с максимальным временем жизни (TTL) 87600 часов.
Success! Tuned the secrets engine at: pki/
vagrant@vagrant:~$ vault write -field=certificate pki/root/generate/internal \     #Создайте корневой сертификат и сохраните его в формате CA_cert.crt.
>      common_name="example.com" \
>      ttl=87600h > CA_cert.crt
vagrant@vagrant:~$ vault write pki/config/urls \    #Настройте URL-адреса CA и CRL.
>      issuing_certificates="$VAULT_ADDR/v1/pki/ca" \
>      crl_distribution_points="$VAULT_ADDR/v1/pki/crl"
Success! Data written to: pki/config/urls
vagrant@vagrant:~$
```

Шаг 2. Создайте промежуточный ЦС


```bash
vagrant@vagrant:~$ vault secrets enable -path=pki_int pki  # Во-первых, активируйте pki механизм секретов на pki_intпути.
Success! Enabled the pki secrets engine at: pki_int/
vagrant@vagrant:~$ vault secrets tune -max-lease-ttl=43800h pki_int  # Настройте pki_intмеханизм секретов для выдачи сертификатов с максимальным временем жизни (TTL) 43800 часов.
Success! Tuned the secrets engine at: pki_int/
vagrant@vagrant:~$ vault write -format=json pki_int/intermediate/generate/internal \  # Выполните следующую команду, чтобы сгенерировать промежуточное звено и сохранить CSR как pki_intermediate.csr.
common_>      common_name="example.com Intermediate Authority" \
>      | jq -r '.data.csr' > pki_intermediate.csr
vagrant@vagrant:~$ vault write -format=json pki/root/sign-intermediate csr=@pki_intermediate.csr \  # Подпишите промежуточный сертификат закрытым ключом корневого ЦС и сохраните сгенерированный сертификат как intermediate.cert.pem.
>      format=pem_bundle ttl="43800h" \
>      | jq -r '.data.certificate' > intermediate.cert.pem
vagrant@vagrant:~$ vault write pki_int/intermediate/set-signed certificate=@intermediate.cert.pem  # После подписания CSR и возврата сертификата корневым центром сертификации его можно импортировать обратно в Vault.
Success! Data written to: pki_int/intermediate/set-signed
vagrant@vagrant:~$

```

Шаг 3. Создайте роль

Роль - это логическое имя, которое отображается на политику, используемую для создания этих учетных данных. Он позволяет параметрам конфигурации управлять общими именами сертификатов, альтернативными именами, ключами, для которых они действительны, и т. Д.

* allowed_domains - Задает домены роли (используется allow_bare_domainsи allow-subdomainsопции)
* allow_bare_domains - Указывает, могут ли клиенты запрашивать сертификаты, соответствующие значению самих доменов.
* allow_subdomains - Указывает, могут ли клиенты запрашивать сертификаты с CN, которые являются поддоменами CN, разрешенных другими параметрами ролей (ПРИМЕЧАНИЕ. Это включает поддомены с подстановочными знаками).
* allow_glob_domains - Позволяет именам, указанным в allowed_domains, содержать шаблоны глобусов (например, ftp * .example.com)

```bash

vagrant@vagrant:~$ vault write pki_int/roles/example-dot-com \
ns="example.com>      allowed_domains="example.com" \
>      allow_subdomains=true \
>      max_ttl="720h"
Success! Data written to: pki_int/roles/example-dot-com
```

```bash
vagrant@vagrant:~$ vault write pki_int/issue/example-dot-com common_name="test.example.com" ttl="720h"
Key                 Value
---                 -----
ca_chain            [-----BEGIN CERTIFICATE-----
MIIDpjCCAo6gAwIBAgIULoyY5iXS8vTb0iWVXQDYHwdHB28wDQYJKoZIhvcNAQEL
BQAwFjEUMBIGA1UEAxMLZXhhbXBsZS5jb20wHhcNMjExMjIxMTkzODM1WhcNMjYx
MjIwMTkzOTA1WjAtMSswKQYDVQQDEyJleGFtcGxlLmNvbSBJbnRlcm1lZGlhdGUg
QXV0aG9yaXR5MIIBIjANBgkqhkiG9w0BAQEFAAOCAQ8AMIIBCgKCAQEAui9bvVFL
Wx0iliZv1QKcW1RQXaOBv4V/9Ri8und4wlUuYq6u/fqpnreh/XPDGHoWdQ27Z+oQ
YsFHNs9IwC1qhvHGUQhLml8naMu4Lvapg0afESWSvTtKdPbsw6P9b3q83lWkRF9L
c85I0loSxK5mW1zBdmlp60bNR2pGlgtUtMmTJCuhSaN+37ReySvKPEyHDcut3lKQ
kJI/AiBBqMS9Z28AhhKL3PY3RsV3aTVz1I63vqd6sVT0wax9AbrCEUE3/J2TSeQM
mgASpX3TC7G3WSDyNP0y+K/iGWkwNY/yMrr2ufNQ80PcnlrsqOKdTgw+yNMHYrZ/
0dm4miVZ5BbRUwIDAQABo4HUMIHRMA4GA1UdDwEB/wQEAwIBBjAPBgNVHRMBAf8E
BTADAQH/MB0GA1UdDgQWBBQAh6IVlVnfsCOfiL/+MA4IL3BHcjAfBgNVHSMEGDAW
gBS8idQDEFgeO06oDYTNdPwCJ2+XCjA7BggrBgEFBQcBAQQvMC0wKwYIKwYBBQUH
MAKGH2h0dHA6Ly8xMjcuMC4wLjE6ODIwMC92MS9wa2kvY2EwMQYDVR0fBCowKDAm
oCSgIoYgaHR0cDovLzEyNy4wLjAuMTo4MjAwL3YxL3BraS9jcmwwDQYJKoZIhvcN
AQELBQADggEBAM+HS9UoCQihgWt5g+SFXLe7/c1K33wz1uHiJUMdcFKqa/sO5j5/
0O6pdWBQNhvnUYyYg++jRveC6zWiun8ImLN6H08olBEFtA0uO/jrqZ4IrKanxlEk
XRQ6sKCvVNV+Jvz//mJzJCgx8/pwFamd8R+O21sXItyDoi6pSqARWN5dXqQO5A5j
TjokvqM7d2+s7/MSVy6VpGXL+IcXFnUYIhniw2G/SU22dYxYfLUrM/U/uZQxGxCg
+YqGk/m5oZWWeeOOs3on8PqHPl+415JSalf34bnlnhVn9yAYPfei6Dok3qHZ+mG2
9H0b+tkz3/wZ3drq+u+q6rKNXcRArEqXR+A=
-----END CERTIFICATE-----]
certificate         -----BEGIN CERTIFICATE-----
MIIDZjCCAk6gAwIBAgIUI4fGtIy6qweFSvDLm1PHJ3uppNEwDQYJKoZIhvcNAQEL
BQAwLTErMCkGA1UEAxMiZXhhbXBsZS5jb20gSW50ZXJtZWRpYXRlIEF1dGhvcml0
eTAeFw0yMTEyMjExOTM5MzRaFw0yMTEyMjIxOTQwMDNaMBsxGTAXBgNVBAMTEHRl
c3QuZXhhbXBsZS5jb20wggEiMA0GCSqGSIb3DQEBAQUAA4IBDwAwggEKAoIBAQDQ
nmf61tiOwaNf2xS3wjaVnpY8xxKl+7aQ/SP8lvdW68XQhV9FB5O9+6pG4TDqC8hU
BMFZrDCaoP52TvHm7gByblgUAzu38SPLEDBoU24kxzTuj+XN8/d4bnIHRiJaXH7i
0qOxjuLewrXV2iZy5TvxEPeIew7SCfcJ1IJTgfGtqjMxVRpNkXVROSNI010T6nfm
urjth0w+1b+tlp75WF0eCGNIeIK3tKbKAKtb4gc2cUsZp1RcTiNLEMiFrKLa8bhL
UAIXEgP3fSPCCCC9dajnlr5eijy2LkbbJAslgnxdW/W2hbRF6IUvPl+n1rG3Dq4d
MvTDst60IMuDwSlYWZjzAgMBAAGjgY8wgYwwDgYDVR0PAQH/BAQDAgOoMB0GA1Ud
JQQWMBQGCCsGAQUFBwMBBggrBgEFBQcDAjAdBgNVHQ4EFgQU3ZFzPR13qRM5gbbw
YYzfQtjmAGIwHwYDVR0jBBgwFoAUAIeiFZVZ37Ajn4i//jAOCC9wR3IwGwYDVR0R
BBQwEoIQdGVzdC5leGFtcGxlLmNvbTANBgkqhkiG9w0BAQsFAAOCAQEAUSXPLfhc
Hjr+OBtuMS6QO4rdtL8gLvVr7I60UikIi6XX0D9T/0Qz1qDukmUNgKyZ8OtrbCjL
H2tomd7mE712gZtvRlN24PcPqshIDz1IyU1dMGmktgwjJinXCabX7+HG+OSVG3Yw
28meb5xLHUASjEiCKo0VxVZXVZgAvhYxPQakjfWolDZIryenDrka0ylXEnP+s7O3
F8qM9xLtifjvrLZGBiHh+x3VIoZPG5xNfDlJOTP6N/gbP/+eP0qUispoOZ/Zty7p
6bMglIcj3eKTRVZpB0KlE9/SjaALNvbtg2fH95HUFY/9lkBKJ4viqdeVF3/6ddFe
fqP0UhBksX8hHw==
-----END CERTIFICATE-----
expiration          1640202003
issuing_ca          -----BEGIN CERTIFICATE-----
MIIDpjCCAo6gAwIBAgIULoyY5iXS8vTb0iWVXQDYHwdHB28wDQYJKoZIhvcNAQEL
BQAwFjEUMBIGA1UEAxMLZXhhbXBsZS5jb20wHhcNMjExMjIxMTkzODM1WhcNMjYx
MjIwMTkzOTA1WjAtMSswKQYDVQQDEyJleGFtcGxlLmNvbSBJbnRlcm1lZGlhdGUg
QXV0aG9yaXR5MIIBIjANBgkqhkiG9w0BAQEFAAOCAQ8AMIIBCgKCAQEAui9bvVFL
Wx0iliZv1QKcW1RQXaOBv4V/9Ri8und4wlUuYq6u/fqpnreh/XPDGHoWdQ27Z+oQ
YsFHNs9IwC1qhvHGUQhLml8naMu4Lvapg0afESWSvTtKdPbsw6P9b3q83lWkRF9L
c85I0loSxK5mW1zBdmlp60bNR2pGlgtUtMmTJCuhSaN+37ReySvKPEyHDcut3lKQ
kJI/AiBBqMS9Z28AhhKL3PY3RsV3aTVz1I63vqd6sVT0wax9AbrCEUE3/J2TSeQM
mgASpX3TC7G3WSDyNP0y+K/iGWkwNY/yMrr2ufNQ80PcnlrsqOKdTgw+yNMHYrZ/
0dm4miVZ5BbRUwIDAQABo4HUMIHRMA4GA1UdDwEB/wQEAwIBBjAPBgNVHRMBAf8E
BTADAQH/MB0GA1UdDgQWBBQAh6IVlVnfsCOfiL/+MA4IL3BHcjAfBgNVHSMEGDAW
gBS8idQDEFgeO06oDYTNdPwCJ2+XCjA7BggrBgEFBQcBAQQvMC0wKwYIKwYBBQUH
MAKGH2h0dHA6Ly8xMjcuMC4wLjE6ODIwMC92MS9wa2kvY2EwMQYDVR0fBCowKDAm
oCSgIoYgaHR0cDovLzEyNy4wLjAuMTo4MjAwL3YxL3BraS9jcmwwDQYJKoZIhvcN
AQELBQADggEBAM+HS9UoCQihgWt5g+SFXLe7/c1K33wz1uHiJUMdcFKqa/sO5j5/
0O6pdWBQNhvnUYyYg++jRveC6zWiun8ImLN6H08olBEFtA0uO/jrqZ4IrKanxlEk
XRQ6sKCvVNV+Jvz//mJzJCgx8/pwFamd8R+O21sXItyDoi6pSqARWN5dXqQO5A5j
TjokvqM7d2+s7/MSVy6VpGXL+IcXFnUYIhniw2G/SU22dYxYfLUrM/U/uZQxGxCg
+YqGk/m5oZWWeeOOs3on8PqHPl+415JSalf34bnlnhVn9yAYPfei6Dok3qHZ+mG2
9H0b+tkz3/wZ3drq+u+q6rKNXcRArEqXR+A=
-----END CERTIFICATE-----
private_key         -----BEGIN RSA PRIVATE KEY-----
MIIEogIBAAKCAQEA0J5n+tbYjsGjX9sUt8I2lZ6WPMcSpfu2kP0j/Jb3VuvF0IVf
RQeTvfuqRuEw6gvIVATBWawwmqD+dk7x5u4Acm5YFAM7t/EjyxAwaFNuJMc07o/l
zfP3eG5yB0YiWlx+4tKjsY7i3sK11domcuU78RD3iHsO0gn3CdSCU4HxraozMVUa
TZF1UTkjSNNdE+p35rq47YdMPtW/rZae+VhdHghjSHiCt7SmygCrW+IHNnFLGadU
XE4jSxDIhayi2vG4S1ACFxID930jwgggvXWo55a+Xoo8ti5G2yQLJYJ8XVv1toW0
ReiFLz5fp9axtw6uHTL0w7LetCDLg8EpWFmY8wIDAQABAoIBAEomY5L21lY3YVhq
yDxgXYJkscW8hFZW15dFQos/JX7RoqfxH+GrRcobL3SuCfLGaVfjsM2stEHhw1BN
0xFfuuzxZ4EFfS5uGWjNwkbykI2l+JxqrSW891Lo/sKzHPa9NaRtdh1gSicfUf5G
4a+o5ttSSolKJARn4lSuIfyIjVxKJCi0jlNQP5WYbPzVMzc0ie5EkOyfzUop3iQx
/jWfpnzxwVGynkMB3hre+CS433ZO5zJnG98M+BTPpmNQFXaMSTcW4byXwHa2QIBS
MGCW2ENbbnMq+eRrADjMx5Qh6Se3YXHQjtFHDNN4BwJ8P1StN6/7XmM+bC9xqg9H
9SYq4lECgYEA8LkiDjUTUJG+zuXKU8ObKKD4PhLPSWQk4w1rJZCEqg23PDoxMkAi
g6taveFf9LKPa4eXLdxNFsBG1AVj7+5YmSaQKwzY9O0SzNJkON+e8u5op+boaTVG
7yWr0Kg7tEnl07glEMjeYU0xJRvWbm/jPIynUl/B+8OcZd+tZRI4CtcCgYEA3dux
0Oe8KTYrU9LSs5ZtrGR7bX/eK1HUy62vnO7n63UofKErxiEwOfiATxKesCoAoO95
bl9gbeph6Tb/bm2I+BH0msx8qEeOPc6H6unISFt6QBGV8gSvdnBgM0UZ+Oti3Znd
BBeJsD2y0fBY9CmDRXWyRV1cLTurCw0kF9F2G0UCgYBEn9J4kGLoNhRaMSIDzSCf
bXw3Xe9f8b+eRsYD2HYpcG2LKt+5fpUYSjI9LVHIlVrTgbz0YvhLAxdt+/dof98c
EeVJkIkNEcSpRVUy7XUHtjV5oBcjXv/Qz0Fy43LZy7NMLxPJ/CmBPZtEA/KWWylB
qqtMjWXkmvgsBp8M0CvP0wKBgA7BmQ8jCrHXB4S4xgjp3XuU51y+H4HUHIo7kC2o
+kX/8eRwN+AivvlsHOSlgWaINKh/pR0BPQGykDaZ0N2AbZbKYvGU3km3EK/tt76Z
NBgT9YqjYMzLIPyItu1SEEstJNNGc5vdEOxm/ijWiqQayr9o96WDcykTCepfQmJP
LULdAoGAVFeiU09A86/iAuDUyLGCpnh+XXaZ+v/kY0Sbu1uuASb5OmhLbQc0jA9t
FsnDI1fmrTQPJV1pUymlnxDtIJP70werfGiIj5d0oC07HDkEsj22A73bvvck5ciN
aIHxWHMcpgcl+aR1Kculf1P+x7wC9Fx1IzxajF+54fEvs4BVbsU=
-----END RSA PRIVATE KEY-----
private_key_type    rsa
serial_number       23:87:c6:b4:8c:ba:ab:07:85:4a:f0:cb:9b:53:c7:27:7b:a9:a4:d1
```

>5. Установите корневой сертификат созданного центра сертификации в доверенные в хостовой системе.

![](cert/install_CA.png)

>6. Установите nginx.

```bash

vagrant@vagrant:~$ sudo apt install nginx
Reading package lists... Done
Building dependency tree
Reading state information... Done
The following additional packages will be installed:
  fontconfig-config fonts-dejavu-core libfontconfig1 libgd3 libjbig0 libjpeg-turbo8 libjpeg8
  libnginx-mod-http-image-filter libnginx-mod-http-xslt-filter libnginx-mod-mail libnginx-mod-stream libtiff5 libwebp6
  libx11-6 libx11-data libxcb1 libxpm4 nginx-common nginx-core
Suggested packages:
  libgd-tools fcgiwrap nginx-doc ssl-cert
The following NEW packages will be installed:
  fontconfig-config fonts-dejavu-core libfontconfig1 libgd3 libjbig0 libjpeg-turbo8 libjpeg8
  libnginx-mod-http-image-filter libnginx-mod-http-xslt-filter libnginx-mod-mail libnginx-mod-stream libtiff5 libwebp6
  libx11-6 libx11-data libxcb1 libxpm4 nginx nginx-common nginx-core
0 upgraded, 20 newly installed, 0 to remove and 108 not upgraded.
Preparing to unpack .../19-nginx_1.18.0-0ubuntu1.2_all.deb ...
Unpacking nginx (1.18.0-0ubuntu1.2) ...
Setting up libxcb1:amd64 (1.14-2) ...
Setting up nginx-common (1.18.0-0ubuntu1.2) ...
Created symlink /etc/systemd/system/multi-user.target.wants/nginx.service → /lib/systemd/system/nginx.service.
Setting up libjbig0:amd64 (2.1-3.1build1) ...
Setting up libnginx-mod-http-xslt-filter (1.18.0-0ubuntu1.2) ...
Setting up libx11-data (2:1.6.9-2ubuntu1.2) ...
Setting up libwebp6:amd64 (0.6.1-2ubuntu0.20.04.1) ...
Setting up fonts-dejavu-core (2.37-1) ...
Setting up libjpeg-turbo8:amd64 (2.0.3-0ubuntu1.20.04.1) ...
Setting up libx11-6:amd64 (2:1.6.9-2ubuntu1.2) ...
Setting up libjpeg8:amd64 (8c-2ubuntu8) ...
Setting up libnginx-mod-mail (1.18.0-0ubuntu1.2) ...
Setting up libxpm4:amd64 (1:3.5.12-1) ...
Setting up fontconfig-config (2.13.1-2ubuntu3) ...
Setting up libnginx-mod-stream (1.18.0-0ubuntu1.2) ...
Setting up libtiff5:amd64 (4.1.0+git191117-2ubuntu0.20.04.2) ...
Setting up libfontconfig1:amd64 (2.13.1-2ubuntu3) ...
Setting up libgd3:amd64 (2.2.5-5.2ubuntu2.1) ...
Setting up libnginx-mod-http-image-filter (1.18.0-0ubuntu1.2) ...
Setting up nginx-core (1.18.0-0ubuntu1.2) ...
Setting up nginx (1.18.0-0ubuntu1.2) ...
Processing triggers for ufw (0.36-6) ...
Processing triggers for systemd (245.4-4ubuntu3.11) ...
Processing triggers for man-db (2.9.1-1) ...
Processing triggers for libc-bin (2.31-0ubuntu9.2) ...
vagrant@vagrant:~$

```

Откроем порты:

* Nginx Full: этот профиль открывает порт 80 (обычный веб-трафик без шифрования) и порт 443 (трафик с шифрованием TLS/SSL)
* Nginx HTTP: этот профиль открывает только порт 80 (обычный веб-трафик без шифрования)
* Nginx HTTPS: этот профиль открывает только порт 443 (трафик с шифрованием TLS/SSL)

```bash
vagrant@vagrant:~$ sudo ufw app list
Available applications:
  Nginx Full
  Nginx HTTP
  Nginx HTTPS
  OpenSSH
vagrant@vagrant:~$ sudo ufw allow 'Nginx Full'
Rules updated
Rules updated (v6)
vagrant@vagrant:~$

vagrant@vagrant:~$ sudo ufw status
Status: active

To                         Action      From
--                         ------      ----
22/tcp                     ALLOW       Anywhere
443/tcp                    ALLOW       Anywhere
Nginx Full                 ALLOW       Anywhere
22/tcp (v6)                ALLOW       Anywhere (v6)
443/tcp (v6)               ALLOW       Anywhere (v6)
Nginx Full (v6)            ALLOW       Anywhere (v6)

```

Проверяем статус nginx:

```bash
vagrant@vagrant:~$ sudo systemctl status nginx
● nginx.service - A high performance web server and a reverse proxy server
     Loaded: loaded (/lib/systemd/system/nginx.service; enabled; vendor preset: enabled)
     Active: active (running) since Tue 2021-12-21 18:38:42 UTC; 4min 8s ago
       Docs: man:nginx(8)
   Main PID: 2006 (nginx)
      Tasks: 5 (limit: 2278)
     Memory: 6.8M
     CGroup: /system.slice/nginx.service
             ├─2006 nginx: master process /usr/sbin/nginx -g daemon on; master_process on;
             ├─2007 nginx: worker process
             ├─2008 nginx: worker process
             ├─2009 nginx: worker process
             └─2010 nginx: worker process

Dec 21 18:38:42 vagrant systemd[1]: Starting A high performance web server and a reverse proxy server...
Dec 21 18:38:42 vagrant systemd[1]: Started A high performance web server and a reverse proxy server.
vagrant@vagrant:~$
```


>7. По инструкции ([ссылка](https://nginx.org/en/docs/http/configuring_https_servers.html)) настройте nginx на https, используя ранее подготовленный сертификат:
>- можно использовать стандартную стартовую страницу nginx для демонстрации работы сервера;
>- можно использовать и другой html файл, сделанный вами;

Добавил сертификаты в директорию `/home/ssl` и поправил конфиг `/etc/nginx/sites-enabled`
```bash
root@vagrant:/home/ssl touch tls.crt
root@vagrant:/home/ssl touch tls.key

...............

server {
        listen 80 default_server;
        listen [::]:80 default_server;

        # SSL configuration
        #
        listen 443 ssl default_server;
        listen [::]:443 ssl default_server;
        ssl_certificate     /home/ssl/tls.crt;
        ssl_certificate_key /home/ssl/tls.key;
        ssl_protocols       TLSv1 TLSv1.1 TLSv1.2;
......................
}
```
>8. Откройте в браузере на хосте https адрес страницы, которую обслуживает сервер nginx.

Поправил хосты на хостовой машине на

`127.0.0.1     test.example.com`

![](cert/test.png)



>9. Создайте скрипт, который будет генерировать новый сертификат в vault:
>- генерируем новый сертификат так, чтобы не переписывать конфиг nginx;
>- перезапускаем nginx для применения нового сертификата.

```bash
..............
Прошу дать напутствие по идеи написания данного скрипта - так как мы запускаем в dev режиме vault без распечатывания и развертывания это получается какие то пляски с бубном… выпуск сертификата домена выдает только текстовый вариант ключа в pam формате.

Представляю так:
Условие, dev сервер не выключался с момента создания CA сертификата

В отдельной сессии уже запущен сервер vault server -dev -dev-root-token-id root
Экспортирована переменная среда для vault интерфейса командной строки для адресации сервера Vault. export VAULT_ADDR=http://127.0.0.1:8200
Экспортирована переменная среда для vaultCLI для аутентификации на сервере Vault. export VAULT_TOKEN=root
Уже выпущены с длительным сроком корневой и промежуточный сертификаты.
Наше действие:

Удаляем сертификаты с истекшим сроком vault write pki_int/tidy tidy_cert_store=true tidy_revoked_certs=true
Запрашиваем новый сертификат и пишем в файл вывода vault write pki_int/issue/example-dot-com common_name="test1.example.com" ttl="24h" | tee /home/ssl/output.crt
А вот далее у меня проблемы… как этот корявый “вывод” разбить на строки и сохранить в key и crt по пути /home/ssl/. В голову приходит только cat, но как описать что нужно брать… и какие символы удалить лишние.

Может я перемудрил и есть какие то более простые решения?

# save cert
#/home/ssl/tls.crt
#/home/ssl/tls.key

# nginx restart
service nginx restart


```




>10. Поместите скрипт в crontab, чтобы сертификат обновлялся какого-то числа каждого месяца в удобное для вас время.

```bash
sudo nano /usr/local/bin/script.sh
sudo chmod ugo+x /usr/local/bin/script.sh
vagrant@vagrant:/home/ssl$ crontab -e
```
Добавили задачу
```
* * 15 * * /usr/local/bin/script.sh
```

## Результат

Результатом курсовой работы должны быть снимки экрана или текст:

- Процесс установки и настройки ufw +
- Процесс установки и выпуска сертификата с помощью hashicorp vault +
- Процесс установки и настройки сервера nginx +
- Страница сервера nginx в браузере хоста не содержит предупреждений +
- Скрипт генерации нового сертификата работает (сертификат сервера ngnix должен быть "зеленым") ???
- Crontab работает (выберите число и время так, чтобы показать что crontab запускается и делает что надо) ???