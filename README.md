# Configurando Acesso e Provisionamento de ONU na OLT Huawei

* Os comandos são:

> enable (acesso para modo privilegiado)

> configure (pode realizar configurações)

> dispaly board 0 (apresentar os slots, e se já estão Normal que é o esperado)

> board confirm 0 (configure como Normal todas as placas do frame 0)

* Parte de Gerência (existe uma porta específica para isso)

> vlan 3000 standard

> interface vlanif 3000

> ip address X.X.X.X/XX description "GERENCIA" (Endereço de IP e Máscara)

> quit

> ip route-static 0.0.0.0 0.0.0.0 X.X.X.X (Gateway)

> vlan 1100 smart (Smart é para Internet, e Standard, gerência)

> interface gpon 0/1 (Entrando na interface localizada em frame 0 e slot 1)

> port 0 ont-auto-find enable

> port 1 ont-auto-find enable

> quit

> dba-profile add type4 max 102400

> ont-srvprofile gpon profile-id 3 profile-name "Y" (Y é o modelo do dispositivo, 3 é um ID com liberdade de criação)

> ont-port eth 1 (porta que está com a fibra óptica)

> port vlan eth 1 translation 1100 user-vlan 1100

> commit

> quit

> ont-lineprofile gpon profile-id X profile-name "Y" (X é o ID do dba-profile e, Y, nome do modelo da ONU - Chamada de ONT no Huawey)

> tcont 1 dba-profile-id X

> gem add 126 eth tcont 1 (126 é um ID com liberdade de criação pelo usuário)

> gem mapping 126 0 vlan 1100

> commit

> quit

> interface gpon 0/1

> display ont autofind X (X pode ser tudo - All ou a Porta)

> ont add 1 sn-auth SERIAL_NUMBER omci ont-lineprofile-id X ont-srvprofile-id Y description "DESCRIPTION OF TEST"

> ont port native-vlan 1 0 eth 1 vlan 1100

> quit

> service-port vlan 1100 gpon 0/1/1 ont 0 gemport 126 multi-service user-vlan 1100 tag-transform translate

> port vlan X 0/2 0 (X é o ID da porta)
