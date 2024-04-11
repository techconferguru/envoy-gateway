                  - name: envoy.filters.http.jwt_authn
                    typed_config:
                      '@type': "type.googleapis.com/envoy.extensions.filters.http.jwt_authn.v3.JwtAuthentication"
                      providers:
                        auth_keycloak:
                          issuer: http://keycloak.localhost:8088/realms/Envoy
                          remote_jwks:
                            http_uri:
                              uri: http://keycloak:8080/realms/Envoy/protocol/openid-connect/certs
                              cluster: keycloak-cluster
                              timeout: 5s
                            cache_duration:
                              seconds: 300
                      rules:
                        - match: {prefix: "/"}
                          requires: {provider_name: auth_keycloak}












                - name: keycloak-cluster
                  connect_timeout: 100s
                  type: LOGICAL_DNS
                  dns_lookup_family: V4_ONLY
                  lb_policy: ROUND_ROBIN
                  load_assignment:
                    cluster_name: keycloak-cluster
                    endpoints:
                      - lb_endpoints:
                          - endpoint:
                              address:
                                socket_address:
                                  address: keycloak #ip-address or container name
                                  port_value: 8080