## ELK Stack ansible sample repository

This is based in ALA ELK inventories.

To simplify this is a simple cluster with one node. Initially security and SSL/TLS are not enabled and should be enabled after the first deployment.

To use this:

1. Edit the `inventory.ini` and addapt your main node domain and IP, nodes to monitor...
2. edit: `roles/elk_forwarder/tasks/main.yml`
3. `ansible-playbook -i inventory.ini ./elasticsearch.yml --become`
4. In the node: `service elasticsearch start` and verify that elastic is running
5. Install kibana: `ansible-playbook -i inventory.ini ./kibana.yml --become`
6. You can start monitoring elk: `ansible-playbook -i inventory.ini ./elk_forwarder.yml --become --limit nbn-elk-1`
7. adjust `./roles/logstash/files/templates/logstash-solr-template.json` 
8. Install logstash: `ansible-playbook -i inventory.ini ./logstash.yml --become`
9. Exec to download the geoip db (be sure that your maxmind account are correct in the inventory): `/usr/bin/geoipupdate --config-file /etc/GeoIP.conf --database-directory /usr/share/GeoIP`
10. `ansible-playbook -i inventory.ini ./elk_forwarder.yml --become`
11. Optional: Setup SSL/TLS component by component
https://www.elastic.co/blog/configuring-ssl-tls-and-https-to-secure-elasticsearch-kibana-beats-and-logstash
12. jvm adjusts (`grep -r "when: 0" .` to verify disabled tasks)
13. Configure jenkins curator, for instance, using `geerlingguy.elasticsearch-curator` role. Sample task: 
```
   - role: geerlingguy.elasticsearch-curator
      elasticsearch_curator_cron_jobs:
        - {
        name: "Delete old elasticsearch indices.",
        job: "/usr/local/bin/curator delete --older-than 7",
        minute: "0",
        hour: "1"
        }
        - {
        name: "Close old elasticsearch indices.",
        job: "/usr/local/bin/curator close --older-than 2",
        minute: "30",
        hour: "1"
        }
      tags: elastic_curator
```
