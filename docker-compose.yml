version: '3'
services:
  mongo:
    image: mongo:5.0
    volumes:
      - mongodb_data:/data/db
      - mongodb_configdb:/data/configdb
    networks:
      network_qnet-static:
        ipv4_address: 10.0.2.17
    mac_address: fe:ed:fa:ce:f0:0d
    ports:
      - 27017:27017
  opensearch:
    image: "opensearchproject/opensearch:2.5.0"
    environment:
      - "OPENSEARCH_JAVA_OPTS=-Xms1g -Xmx1g"
      - "bootstrap.memory_lock=true"
      - "discovery.type=single-node"
      - "action.auto_create_index=false"
      - "plugins.security.ssl.http.enabled=false"
      - "DISABLE_SECURITY_PLUGIN=true"
#      - "plugins.security.disabled=true"
#      - "compatibility.override_main_response_version=true"
      - network.host=10.0.2.16
      - http.host=10.0.2.16
      - transport.host=10.0.2.16
    ulimits:
      memlock:
        hard: -1
        soft: -1
      nofile:
        soft: 65536
        hard: 65536
    volumes:
      - "os_data:/usr/share/opensearch/data"
    networks:
      network_qnet-static:
        ipv4_address: 10.0.2.16
    mac_address: fe:ed:fa:ce:f0:1d
    restart: "on-failure"

  graylog:
    image: graylog/graylog:5.0
    networks:
      network_qnet-static:
        ipv4_address: 10.0.2.15
    mac_address: fe:ed:fa:ce:f1:1d
    volumes:
      - graylog_data:/usr/share/graylog/data
      - graylog_plugin:/usr/share/graylog/plugin
    environment:
      # CHANGE ME (must be at least 16 characters)!
      - TZ=America/Chicago
      - GRAYLOG_PASSWORD_SECRET=somepasswordpepper
      - GRAYLOG_ROOT_TIMEZONE=America/Chicago
      # Password: admin
      - GRAYLOG_ROOT_PASSWORD_SHA2=8c6976e5b541041thesearenotthedroidsyouarelookingforb448a918
      - GRAYLOG_HTTP_EXTERNAL_URI=http://10.0.2.15:9000/
    entrypoint: /usr/bin/tini -- wait-for-it 10.0.2.16:9200 --  /docker-entrypoint.sh
    restart: always
    depends_on:
      - mongo
      - opensearch
    ports:
      # Graylog web interface and REST API
      - 80:80
      # Syslog TCP
      - 1514:1514
      # Syslog UDP
      - 1514:1514/udp
      # GELF TCP
      - 12201:12201
      # GELF UDP
      - 12201:12201/udp
volumes:
  mongodb_data:
  mongodb_configdb:
#  es_data:
  os_data:
  graylog_data:
  graylog_plugin:
networks:
  default:
    external: true
    name: network_qnet-static
  network_qnet-static:
    external: true
