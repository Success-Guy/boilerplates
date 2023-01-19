# Monitoring

## With Prometheus and graphana

### USing Container 

```
docker-compose up -d
```

### To install prometheus
```
ansible-playbook -i inventory.txt configure-prometheus.yml
```

Cpoy the content of prometheus.yml to /etc/prometheus/prometheus.yml

## Documentation

https://github.com/google/cadvisor/

https://prometheus.io/docs/prometheus/latest/installation/