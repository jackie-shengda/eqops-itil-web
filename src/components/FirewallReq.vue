<template>
    <div style="margin: 10px">
        Display border
        <i-switch v-model="showBorder" style="margin-right: 5px"></i-switch>
        Display stripe
        <i-switch v-model="showStripe" style="margin-right: 5px"></i-switch>
        Display index
        <i-switch v-model="showIndex" style="margin-right: 5px"></i-switch>
        Display multi choice
        <i-switch v-model="showCheckbox" style="margin-right: 5px"></i-switch>
        Display header
        <i-switch v-model="showHeader" style="margin-right: 5px"></i-switch>
        Table scrolling
        <i-switch v-model="fixedHeader" style="margin-right: 5px"></i-switch>
        <br>
        <br>
        Table size
        <Radio-group v-model="tableSize" type="button">
            <Radio label="large">large</Radio>
            <Radio label="default">medium(default)</Radio>
            <Radio label="small">small</Radio>
        </Radio-group>
        <Table :border="showBorder"
               :show-header="showHeader"
               :stripe="showStripe"
               :size="tableSize"
               :data="firewall_req_data"
               :columns="firewall_req_data_columns">
            <!-- 源地址选择 -->
            <template slot-scope="{ row, index }" slot="source_ips">
                <Select v-model="editSourceIps"
                        v-if="editIndex === index"
                        filterale
                        multiple>
                    <Option v-for="ip in sourceIpSelect" :value="ip.value" :key="ip.value">{{ip.name}}</Option>
                </Select>
                <p v-else v-for="i in row.source_ips" :key="i">{{ i }}</p>
            </template>

            <!-- 目标地址选择 -->
            <template slot-scope="{ row, index }" slot="destination_ips">
                <Select v-model="editDestinationIps"
                        v-if="editIndex === index"
                        multiple>
                    <Option v-for="ip in sourceIpSelect" :value="ip.value" :key="ip.value">{{ip.name}}</Option>
                </Select>
                <p v-else v-for="i in row.destination_ips" :key="i">{{ i }}</p>
            </template>

            <!--目的端口填写-->
            <template slot-scope="{ row, index }" slot="destination_ports">
                <Input v-model="editDestinationPorts" v-if="editIndex === index"/>
                <span v-else>{{ row.destination_ports }}</span>
            </template>

            <!--协议选择-->
            <template slot-scope="{ row, index }" slot="proxy">
                <Select v-model="editProxy" v-if="editIndex === index">
                    <Option v-for="p in proxySelect" :value="p.value" :key="p.name">{{p.name}}</Option>
                </Select>
                <span v-else>{{row.proxy}}</span>
            </template>

            <!--编辑描述-->
            <template slot-scope="{ row,index}" slot="desc">
                <Input v-model="editDesc" v-if="editIndex === index" type="textarea"/>
                <span v-else>{{row.desc}}</span>
            </template>

            <!--操作-->
            <template slot-scope="{ row, index }" slot="action">
                <div v-if="editIndex === index">
                    <Button @click="handleSave(index)">保存</Button>
                    <Button @click="editIndex = -1">取消</Button>
                </div>
                <div v-else>
                    <Button @click="handleEdit(row,index)">编辑</Button>
                    <Button @click="handleCopy(index)">复制</Button>
                    <Button @click="handleDelete(index)">删除</Button>
                </div>
            </template>
            <tr>新增一行</tr>
        </Table>
    </div>
</template>

<script>
    export default {
        name: 'FirewallReq',
        data() {
            return {
                showBorder: true,   //是否显示表头
                showStripe: false,  //是否显示条纹
                showHeader: true,   //是否显示表头
                showCheckbox: false,//是否显示勾选框
                showIndex: true,    //是否显示索引
                fixedHeader: false,
                tableSize: 'default',   //表格默认大小

                //编辑控制参数
                editIndex: -1,      //当前聚焦的输入框行数
                editSourceIps: [],   //源目标地址编辑
                editSourcePort: 'Any',  //编辑源端口
                editDestinationIps: [], //编辑目的地址
                editDestinationPorts: [],   //编辑目的端口
                editProxy: '',
                editDesc:'',        //编辑描述

                firewall_req_data: [
                    {
                        source_ips: [],
                        source_port: 'Any',
                        destination_ips: [],
                        destination_ports: '',
                        proxy: 'tcp',
                        desc: ''
                    }
                ],

                firewall_req_data_model: {
                    source_ips: [],
                    source_port: 'Any',
                    destination_ips: [],
                    destination_ports: [],
                    proxy: '',
                    desc: ''
                },

                //IP地址选择
                sourceIpSelect: [
                    {
                        name: '10.112.6.11',
                        value: '10.112.6.11'
                    },
                    {
                        name: '10.112.6.12',
                        value: '10.112.6.12'
                    },
                    {
                        name: '10.112.6.13',
                        value: '10.112.6.13'
                    },
                    {
                        name: '10.112.6.14',
                        value: '10.112.6.14'
                    },
                    {
                        name: '196.112.6.14',
                        value: '196.112.6.14'
                    },
                    {
                        name: '197.112.6.14',
                        value: '197.112.6.14'
                    },
                    {
                        name: '198.112.6.14',
                        value: '198.112.6.14'
                    }
                ],

                //协议选择
                proxySelect: [
                    {
                        name: 'tcp',
                        value: 'tcp'
                    },
                    {
                        name: 'udp',
                        value: 'udp'
                    },
                    {
                        name: 'ftp',
                        value: 'ftp'
                    },
                ],

                //表格列
                columns: [
                    {
                        type: 'selection',
                        width: 60,
                        align: 'center'
                    },
                    {
                        title: '源地址',
                        slot: 'source_ips'
                    },
                    {
                        title: '源端口',
                        key: 'source_port'
                    },
                    {
                        title: '目的IP地址',
                        key: 'destination_ips'
                    },
                    {
                        title: '目的端口号',
                        key: 'destination_ports'
                    },
                    {
                        title: '协议',
                        key: 'proxy',
                    },
                    {
                        title: '描述',
                        key: 'desc'
                    },
                    {
                        title: '操作',
                        slot: 'action'
                    }

                ]


            }
        },
        computed: {
            firewall_req_data_columns() {
                let columns = [];
                columns.push({
                    type: 'selection',
                    width: 60,
                    align: 'center'
                })
                columns.push({
                    type: 'index',
                    width: 60,
                    align: 'center'
                });
                columns.push({
                    title: '源IP地址',
                    slot: 'source_ips',
                });
                columns.push({
                    title: '源端口',
                    key: 'source_port',
                });
                columns.push({
                    title: '目的IP地址',
                    slot: 'destination_ips'
                });
                columns.push({
                    title: '目的端口号',
                    slot: 'destination_ports'
                });
                columns.push({
                    title: '协议',
                    slot: 'proxy',
                });
                columns.push({
                    title: '描述',
                    slot: 'desc'
                });
                columns.push({
                    title: '操作',
                    slot: 'action'
                })
                return columns;
            }
        },
        methods: {
            handleEdit(row, index) {
                this.editSourceIps = row.source_ips;
                this.editProxy = row.proxy;
                this.editDestinationIps = row.destination_ips;
                this.editDestinationPorts = row.destination_ports;
                this.editDesc = row.desc;
                this.editIndex = index;
            },
            handleSave(index) {
                this.firewall_req_data[index].source_ips = this.editSourceIps;
                this.firewall_req_data[index].proxy = this.editProxy;
                this.firewall_req_data[index].destination_ips = this.editDestinationIps;
                this.firewall_req_data[index].destination_ports = this.editDestinationPorts;
                this.firewall_req_data[index].desc = this.editDesc;
                console.log(this.firewall_req_data[index].source_ips);
                // console.log(this.editSourceIps);
                this.editIndex = -1;
            },
            handleCopy(index) {
                let temp = this.firewall_req_data_model;
                temp.source_ips = this.firewall_req_data[index].source_ips;
                temp.source_port = this.firewall_req_data[index].source_port;
                temp.destination_ips = this.firewall_req_data[index].destination_ips;
                temp.destination_ports = this.firewall_req_data[index].destination_ports;
                temp.desc = this.firewall_req_data[index].desc;
                temp.proxy = this.firewall_req_data[index].proxy;
                this.firewall_req_data.push(this.firewall_req_data_model);
            },
            handleDelete(index) {
                this.firewall_req_data.splice(index, 1);
            }
        }
    }
</script>
