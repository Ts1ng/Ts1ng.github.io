openssl pkcs12 -in Certificates.p12 -clcerts -nokeys -out output_crt.pem
openssl pkcs12 -in Certificates.p12 -nocerts -nodes -out output_key.pem
cat handoff.json | openssl smime -sign -inkey output_key.pem -signer output_crt.pem -noattr -nodetach -outform DER> apple-app-site-association
