### DEPLOY PROD MODE

# Generate the certs
mkdir -p /opt/vault/{tls,data}

cd /opt/vault/tls

openssl req   -out tls.crt   -new   -keyout tls.key   -newkey rsa:4096   -nodes   -sha256   -x509   -subj "/O=HashiCorp/CN=Vault"   -addext "subjectAltName = IP:<loopbackIP>,DNS:<host>"   -days 3650



cat /etc/vault/vault.hcl
# Full configuration options can be found at https://www.vaultproject.io/docs/configuration

ui = true

storage "file" {
  path = "/opt/vault/data"
}

# HTTPS listener
listener "tcp" {
  address       = "0.0.0.0:8200"
  tls_cert_file = "/opt/vault/tls/tls.crt"
  tls_key_file  = "/opt/vault/tls/tls.key"
}

###############################################

chown vault: /opt/vault/tls/*

service vault start

# make sure DNS record is present, else TLS certificate verification
# will fail

export VAULT_ADDR='https://<hostname>:8200'
export VAULT_CACERT="/opt/vault/tls/tls.crt"

# either visit https://<IP>:8200 and enter values as 5 as number of keys and 3 keys needed to unseal or regenerate keys
# copy the root token & keys
vault operator init

root@mac-saltmaster:/opt/vault/tls# vault status

root@mac-saltmaster:/opt/vault/tls# vault operator unseal --ca-cert=/opt/vault/tls/tls.crt

vault login

# Refer production hardening for more: https://learn.hashicorp.com/tutorials/vault/production-hardening