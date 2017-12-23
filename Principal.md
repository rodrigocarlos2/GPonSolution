
# Provisionando ONU (ONT) em uma porta da OLT Huawei

# Processos Iniciais (Introdutórios)

* enable (modo privilegiado)

* configure (pode realizar comandos de configuração)

* display board 0 (apresenta todas os slots existentes na OLT)

* board confirm 0 (levantar todas as placas, dica: pode ser que alguma esteja com falha (Failed))

# Gerência com Porta Eth

* Tem que estar em modo privilegiado e configuração.

* interface meth 0 (entrar para interface de gerência padrão eth)

* ip address X.X.X.X Y.Y.Y.Y (X é o IP, Y é a máscara. Aqui é definido o IP de gerência)

* Neste comando, não é necessário fazer o commit (aplicar). A Aplicação é automática.

* quit (saída)

* ip route-static 0.0.0.0 0.0.0.0 X.X.X.X (X é o gateway).

* sysman service ssh enable (habilita porta ssh para gerência).

* sysman service telnet enable (habilita porta telnet para gerência. O telnet é usado pelo TopSapp).

# Gerência com Porta Vlan

* vlan 1100 standard (standard é uma categoria de VLAN específica para gerência).

* interface vlanif 1100 (entrando nas configurações da vlan 1100).

* ip address X.X.X.X Y.Y.Y.Y (X é o IP, Y é a máscara. Aqui é definido o IP de gerência).

* quit (saída) 

* ip route-static 0.0.0.0 0.0.0.0 X.X.X.X (X é o gateway).

# Provisionar ONU usando que precisa fazer Unstag

# Caracterizando a ONU

* vlan 1100 smart (Smart é uma categoria para de VLAN específica para Internet).

* interface gpon 0/1 (Entrando na interface frame 0, slot 1).

* Abaixo, você deve habilitar as portas da OLT.

* port 1 ont-auto-find enable

* port 2 ont-auto-find enable

* port 3 ont-auto-find enable

* port 4 ont-auto-find enable

* quit

# Dedicando dba-profile, srv-profile e line-profile

* dba-profile add type4 max 102400 (Garantia de largura de banda de 102400. Isso é importante para manter a disputa justa dos clientes.
Pelo que Leandro falou, eu entendi que clientes que tem maior consumo poderiam ganhar banda de clientes com menores consumo).

* ont-srvprofile gpon profile-id X profile-name "Y" (X é o ID do srvprofile-id que você cria. Você pode escolher o srvprofile-id 
que quiser, entretanto, deve ser diferente para cada modelo. "Y" é o nome do modelo da placa).

* A Virtex trabalha com três modelos de ONUs: uma de 1 porta ethernet e uma fxo (que você adiciona usando pots).

* Agora, você deve habilitar portas dessa ONU. Se a ONU tiver diversas portas, diversas portas devem ser adicionadas.
* Então, vamos considerar para teste, uma ONU com 4 portas:

* port vlan eth 1 translation 1100 user-vlan 1100 (1100 é o ID da VLAN. 1 é o ID da porta ethernet).

* port vlan eth 2 translation 1100 user-vlan 1100 (1100 é o ID da VLAN. 2 é o ID da porta ethernet).

* port vlan eth 3 translation 1100 user-vlan 1100 (1100 é o ID da VLAN. 3 é o ID da porta ethernet).

* port vlan eth 4 translation 1100 user-vlan 1100 (1100 é o ID da VLAN. 4 é o ID da porta ethernet).

* commit (aplicar as alterações)

* quit

* ont-lineprofile gpon profile-id X profile-name "Y" (X é o ID do lineprofile-id que está sendo criado. Existe liberdade de ciração
para isso, só não pode repetir com um lineprofile-id já utilizado. "Y" é o modelo da ONU que está sendo provisionada).

* tcont 1 dba-profile-id X (Criando um tcont identificado pelo ID 1, isso também pode variar. O X é dba-profile-id criado acima.
O dba-profile-id é criado automaticamente).

* gem add 126 eth tcont 1 (126 é um ID para o gem que pode ser criado. O 1 é o número do tcont criado anteriormente).

* gem mapping 126 0 vlan 1100 (126 é o número do gem. 0 é possivelmente um mapping index que foi utilizado em todos. 1100 é o
ID da VLAN).

* commit (aplicar as alterações)

* quit (saída)

* interface gpon 0/1 (entrar na interface gpon 0/1)

* display ont autofind X (X é o ID da porta. Se estiver provisionado, então, não vai ser apresentado. Para desprovisionar, você
deve deleta o service-port (primeiro) e depois ont (ont delete)).

* ont add 1 sn-auth SERIAL_NUMBER omci ont-lineprofile-id X ont-srvprofile-id Y description "DESCRIPTION" (X é lineprofile-id e
Y é srvprofile-id).

* ont port native-vlan 1 0 eth 1 vlan 1100 (1 é a porta. 0 é ONT ID. 1 é a porta da fibra. 1100 é o VLAN ID. Isso só é 
necessário quando se quer fazer uma vlan unstag).

* quit (saída)

* service-port vlan 1100 gpon 0/1/1 ont 0 gemport 126 multi-service user-vlan 1100 tag-transform translate (1100 é o VLAN ID. 0/1/1 -
Frame/Slot/Porta. 126 é o gem).

* port vlan X 0/2 0 (X é o VLAN ID. 0/2 é o local da Controladora. 0 é a porta).
