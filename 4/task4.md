sudo apt update && \
sudo apt install golang-go


echo 'export GOROOT=$HOME/go' >> .bashrc && \
echo 'export PATH=$PATH:$GOROOT/bin' >> .bashrc\


go build -o app app.go

./app&

sudo vim /opt/prometheus/prometheus.yml
scrape_configs:
  - job_name: 'app'
    static_configs:
    - targets: ['localhost:9292']

sudo systemctl restart prometheus