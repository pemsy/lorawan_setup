# lorawan_setup

Install requirements
```
sudo apt install \
    mosquitto \
    mosquitto-clients \
    redis-server \
    redis-tools \
    postgresql
```

PostgreSQL setup
```
sudo -u postgres psql
```

-- create role for authentication
```
create role chirpstack with login password 'L5nUYizwsW9zfx7HhVt7';
```
-- create database
```
create database chirpstack with owner chirpstack;
```

-- change to chirpstack database
```
\c chirpstack
```

-- create pg_trgm extension
```
create extension pg_trgm;
```

-- exit psql
```
\q
```

Setup software repository

```
sudo apt install apt-transport-https dirmngr
```
```
sudo apt-key adv --keyserver keyserver.ubuntu.com --recv-keys 1CE2AFD36DBCCA00

```
```
sudo echo "deb https://artifacts.chirpstack.io/packages/4.x/deb stable main" | sudo tee /etc/apt/sources.list.d/chirpstack.list
```
```
sudo apt update
```

# Install ChirpStack Gateway Bridge
```
sudo apt install chirpstack-gateway-bridge
```

```
# This file contains an example EU868 configuration.
[[regions]]

  # Name is an user-defined identifier for this region.
  name="eu868"

  # Common-name refers to the common-name of this region as defined by
  # the LoRa Alliance.
  common_name="EU868"


  # Gateway configuration.
  [regions.gateway]

    # Force gateways as private.
    #
    # If enabled, gateways can only be used by devices under the same tenant.
    force_gws_private=false


    # Gateway backend configuration.
    [regions.gateway.backend]

      # The enabled backend type.
      enabled="mqtt"

      # MQTT configuration.
      [regions.gateway.backend.mqtt]

        # Event topic template.
        event_topic="eu868/gateway/+/event/+"

        # Command topic template.
        command_topic="eu868/gateway/{{ gateway_id }}/command/{{ command }}"

        # MQTT server (e.g. scheme://host:port where scheme is tcp, ssl or ws)
        server="tcp://$MQTT_BROKER_HOST:1883"

        # Connect with the given username (optional)
        username=""

        # Connect with the given password (optional)
        password=""

        # Quality of service level
        #
        # 0: at most once
        # 1: at least once
        # 2: exactly once
        #
        # Note: an increase of this value will decrease the performance.
        # For more information: https://www.hivemq.com/blog/mqtt-essentials-part-6-mqtt-quality-of-service-levels
        qos=0

        # Clean session
        #
        # Set the "clean session" flag in the connect message when this client
        # connects to an MQTT broker. By setting this flag you are indicating
        # that no messages saved by the broker for this client should be delivered.
        clean_session=false

        # Client ID
        #
        # Set the client id to be used by this client when connecting to the MQTT
        # broker. A client id must be no longer than 23 characters. If left blank,
        # a random id will be generated by ChirpStack.
        client_id=""

        # CA certificate file (optional)
        #
        # Use this when setting up a secure connection (when server uses ssl://...)
        # but the certificate used by the server is not trusted by any CA certificate
        # on the server (e.g. when self generated).
        ca_cert=""

        # TLS certificate file (optional)
        tls_cert=""

        # TLS key file (optional)
        tls_key=""


    # Gateway channel configuration.
    #
    # Note: this configuration is only used in case the gateway is using the
    # ChirpStack Concentratord daemon. In any other case, this configuration 
    # is ignored.
    [[regions.gateway.channels]]
      frequency=868100000
      bandwidth=125000
      modulation="LORA"
      spreading_factors=[7, 8, 9, 10, 11, 12]

    [[regions.gateway.channels]]
      frequency=868300000
      bandwidth=125000
      modulation="LORA"
      spreading_factors=[7, 8, 9, 10, 11, 12]

    [[regions.gateway.channels]]
      frequency=868500000
      bandwidth=125000
      modulation="LORA"
      spreading_factors=[7, 8, 9, 10, 11, 12]

    [[regions.gateway.channels]]
      frequency=867100000
      bandwidth=125000
      modulation="LORA"
      spreading_factors=[7, 8, 9, 10, 11, 12]

    [[regions.gateway.channels]]
      frequency=867300000
      bandwidth=125000
      modulation="LORA"
      spreading_factors=[7, 8, 9, 10, 11, 12]

    [[regions.gateway.channels]]
      frequency=867500000
      bandwidth=125000
      modulation="LORA"
      spreading_factors=[7, 8, 9, 10, 11, 12]

    [[regions.gateway.channels]]
      frequency=867700000
      bandwidth=125000
      modulation="LORA"
      spreading_factors=[7, 8, 9, 10, 11, 12]

    [[regions.gateway.channels]]
      frequency=867900000
      bandwidth=125000
      modulation="LORA"
      spreading_factors=[7, 8, 9, 10, 11, 12]
  
    [[regions.gateway.channels]]
      frequency=868300000
      bandwidth=250000
      modulation="LORA"
      spreading_factors=[7]
    
    [[regions.gateway.channels]]
      frequency=868800000
      bandwidth=125000
      modulation="FSK"
      datarate=50000


  # Region specific network configuration.
  [regions.network]
    
    # Installation margin (dB) used by the ADR engine.
    #
    # A higher number means that the network-server will keep more margin,
    # resulting in a lower data-rate but decreasing the chance that the
    # device gets disconnected because it is unable to reach one of the
    # surrounded gateways.
    installation_margin=10

    # RX window (Class-A).
    #
    # Set this to:
    # 0: RX1 / RX2
    # 1: RX1 only
    # 2: RX2 only
    rx_window=0

    # RX1 delay (1 - 15 seconds).
    rx1_delay=1

    # RX1 data-rate offset
    rx1_dr_offset=0

    # RX2 data-rate
    rx2_dr=0

    # RX2 frequency (Hz)
    rx2_frequency=869525000

    # Prefer RX2 on RX1 data-rate less than.
    #
    # Prefer RX2 over RX1 based on the RX1 data-rate. When the RX1 data-rate
    # is smaller than the configured value, then the Network Server will
    # first try to schedule the downlink for RX2, failing that (e.g. the gateway
    # has already a payload scheduled at the RX2 timing) it will try RX1.
    rx2_prefer_on_rx1_dr_lt=0

    # Prefer RX2 on link budget.
    #
    # When the link-budget is better for RX2 than for RX1, the Network Server will first
    # try to schedule the downlink in RX2, failing that it will try RX1.
    rx2_prefer_on_link_budget=false

    # Downlink TX Power (dBm)
    #
    # When set to -1, the downlink TX Power from the configured band will
    # be used.
    #
    # Please consult the LoRaWAN Regional Parameters and local regulations
    # for valid and legal options. Note that the configured TX Power must be
    # supported by your gateway(s).
    downlink_tx_power=-1

    # ADR is disabled.
    adr_disabled=false

    # Minimum data-rate.
    min_dr=0

    # Maximum data-rate.
    max_dr=5


    # Rejoin-request configuration (LoRaWAN 1.1)
    [regions.network.rejoin_request]

      # Request devices to periodically send rejoin-requests.
      enabled=false

      # The device must send a rejoin-request type 0 at least every 2^(max_count_n + 4)
      # uplink messages. Valid values are 0 to 15.
      max_count_n=0

      # The device must send a rejoin-request type 0 at least every 2^(max_time_n + 10)
      # seconds. Valid values are 0 to 15.
      #
      # 0  = roughly 17 minutes
      # 15 = about 1 year
      max_time_n=0
    

    # Class-B configuration.
    [regions.network.class_b]

      # Ping-slot data-rate. 
      ping_slot_dr=0

      # Ping-slot frequency (Hz)
      #
      # set this to 0 to use the default frequency plan for the configured region
      # (which could be frequency hopping).
      ping_slot_frequency=0


    # Below is the common set of extra channels. Please make sure that these
    # channels are also supported by the gateways.
    [[regions.network.extra_channels]]
    frequency=867100000
    min_dr=0
    max_dr=5

    [[regions.network.extra_channels]]
    frequency=867300000
    min_dr=0
    max_dr=5

    [[regions.network.extra_channels]]
    frequency=867500000
    min_dr=0
    max_dr=5

    [[regions.network.extra_channels]]
    frequency=867700000
    min_dr=0
    max_dr=5

    [[regions.network.extra_channels]]
    frequency=867900000
    min_dr=0
    max_dr=5
```
# Starting

## start chirpstack-gateway-bridge
```
sudo systemctl start chirpstack-gateway-bridge
```

## start chirpstack-gateway-bridge on boot
```
sudo systemctl enable chirpstack-gateway-bridge
```

# Install ChirpStack
```
apt install chirpstack
```
```
nano /etc/chirpstack/chirpstack.toml
```

# Staring
```
## start chirpstack
```
sudo systemctl start chirpstack
```

## start chirpstack on boot
```
sudo systemctl enable chirpstack
```

# Loging
```
sudo journalctl -f -n 100 -u chirpstack
```
