Existing setup: Mysql POD is up and runnning with cluster IP.


----> Setting up Prometheus:

        # kubectl create -f prometheus.yaml -f prometheus-deployment.yaml

----> Prometheus exporters (Mysql exporter + NodeExporter)

1. Setup to be done inside MYsql POD, so that they can scrape the POD and present the data in Prometheus readable format.

      # wget https://github.com/prometheus/node_exporter/releases/download/0.12.0rc3/node_exporter-0.12.0rc3.linux-amd64.tar.gz

      # wget https://github.com/prometheus/mysqld_exporter/releases/download/0.7.1/mysqld_exporter-0.7.1.linux-amd64.tar.gz

      # mkdir /opt/prometheus_exporters

      # tar zxf node_exporter-0.12.0rc3.linux-amd64.tar.gz -C /opt/prometheus_exporters

      # tar zxf mysqld_exporter-0.7.1.linux-amd64.tar.gz -C /opt/prometheus_exporters



2. Start Node exporter. port 9100
      # ./node_exporter &



3. Mysqld exporter setup. Port 9104

      Create .my.cnf and start mysqld_exporter.

      # mysql> GRANT REPLICATION CLIENT, PROCESS ON *.* TO 'prom'@'localhost' identified by '<passwd>';

      # mysql> GRANT SELECT ON performance_schema.* TO 'prom'@'localhost';

      # cd /opt/prometheus_exporters
      # cat << EOF > .my.cnf
      [client]
      user=prom
      password=<passwd>
      EOF

      # ./mysqld_exporter -config.my-cnf=".my.cnf" &









----> Grafana setup

      # kubectl create -f grafana.yaml

    For prometheus integration: Just add the Promtheus Data Source in Grafana UI. 


