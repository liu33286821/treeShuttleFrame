<template>
    <div>
        主页
        <to-dialog :title="title"
                   :list="list"
                   :from_data='list.one.fromData'
                   :defaultProps="{label:'name',children:'children'}"
                   :to_data='list.one.toData'
                   @toggleData="toggleData"
                   @addBtn='add'
                   @removeBtn='remove'
                   height='540px' filter open-all></to-dialog>

        <to-dialog2 :title="title2"
                   :from_data='fromData'
                   :defaultProps="{label:'name',children:'children'}"
                   :to_data='toData'
                   @addBtn='add2'
                   @removeBtn='remove2'
                   height='540px' filter open-all></to-dialog2>
    </div>
</template>

<script>
    import ToDialog from './Dialog4'
    import ToDialog2 from './Dialog3'

    export default {
        name: "index",
        components: {ToDialog, ToDialog2},
        data() {
            return {
                title: [
                    [{title: "未设门店", length: 0}, {title: "未设地区", length: 0}],
                    [{title: "已设门店", length: 0}, {title: "已设地区", length: 0}]
                ],
                title2: [
                    "未设门店","已设门店"
                ],
                fromData: [
                    {
                        id: 1,
                        pid: 0,
                        name: "测试左侧",
                        children: [
                            {
                                id: 2,
                                pid: 1,
                                name: "水电费是打发斯蒂芬斯蒂芬gas噶水电费噶地方死光光",
                                children: []
                            },
                            {
                                id: 3,
                                pid: 1,
                                name: "11-3",
                                children: []
                            },
                            {
                                id: 4,
                                pid: 1,
                                name: "11-4",
                                children: [
                                    {
                                        id: 5,
                                        pid: 4,
                                        name: "11-5",
                                        children: [
                                            {
                                                id: 111,
                                                pid: 5,
                                                name: "11-111",
                                                children: []
                                            }
                                        ]
                                    },
                                    {
                                        id: 6,
                                        pid: 4,
                                        name: "11-6",
                                        children: []
                                    }
                                ]
                            }
                        ]
                    }
                ], // 穿梭框 - 源数据 - 树形
                toData: [
                    {
                        id: 1,
                        pid: 0,
                        name: "测试左侧",
                        children: [
                            {
                                id: 2,
                                pid: 1,
                                name: "水电费是打发斯蒂芬斯蒂芬gas噶水电费噶地方死光光",
                                children: [
                                    {
                                        id: 22222222,
                                        pid: 2,
                                        name: "2222222222",
                                        region: '1',
                                        children: []
                                    }
                                ]
                            }
                        ]
                    }
                ], // 穿梭框 - 目标数据 - 树形
                list: {
                    one: {
                        fromData: [
                            {
                                id: 1,
                                pid: 0,
                                name: "测试左侧",
                                children: [
                                    {
                                        id: 2,
                                        pid: 1,
                                        name: "水电费是打发斯蒂芬斯蒂芬gas噶水电费噶地方死光光",
                                        children: []
                                    },
                                    {
                                        id: 3,
                                        pid: 1,
                                        name: "11-3",
                                        children: []
                                    },
                                    {
                                        id: 4,
                                        pid: 1,
                                        name: "11-4",
                                        children: [
                                            {
                                                id: 5,
                                                pid: 4,
                                                name: "11-5",
                                                children: [
                                                    {
                                                        id: 111,
                                                        pid: 5,
                                                        name: "11-111",
                                                        children: []
                                                    }
                                                ]
                                            },
                                            {
                                                id: 6,
                                                pid: 4,
                                                name: "11-6",
                                                children: []
                                            }
                                        ]
                                    }
                                ]
                            }
                        ], // 穿梭框 - 源数据 - 树形
                        toData: [
                            {
                                id: 1,
                                pid: 0,
                                name: "测试左侧",
                                children: [
                                    {
                                        id: 2,
                                        pid: 1,
                                        name: "水电费是打发斯蒂芬斯蒂芬gas噶水电费噶地方死光光",
                                        children: [
                                            {
                                                id: 22222222,
                                                pid: 2,
                                                name: "2222222222",
                                                region: '1',
                                                children: []
                                            }
                                        ]
                                    }
                                ]
                            }
                        ], // 穿梭框 - 目标数据 - 树形
                    },
                    two: {
                        fromData: [
                            {
                                id: 1,
                                pid: 0,
                                name: "全选",
                                children: [
                                    {
                                        id: 2,
                                        pid: 1,
                                        name: "龙湖物业集团",
                                        children: []
                                    },
                                    {
                                        id: 3,
                                        pid: 1,
                                        name: "重庆龙湖",
                                        children: [
                                            {
                                                id: 5,
                                                pid: 3,
                                                name: "水晶郦城",
                                            },
                                            {
                                                id: 55,
                                                pid: 3,
                                                name: "水晶星座",
                                            },
                                            {
                                                id: 553,
                                                pid: 3,
                                                name: "水晶宫",
                                            }
                                        ]
                                    },
                                    {
                                        id: 4,
                                        pid: 1,
                                        name: "成都龙湖",
                                        children: [
                                            {
                                                id: 555,
                                                pid: 4,
                                                name: "三千龙",
                                            },
                                            {
                                                id: 6,
                                                pid: 4,
                                                name: "三千里",
                                            },
                                            {
                                                id: 655,
                                                pid: 4,
                                                name: "三千集",
                                            }
                                        ]
                                    }
                                ]
                            }
                        ],
                        toData: [
                            {
                                id: 1,
                                pid: 0,
                                name: "全选",
                                children: [
                                    {
                                        id: 2,
                                        pid: 1,
                                        name: "龙湖物业集团",
                                        children: [
                                            {
                                                id: 101,
                                                pid: 2,
                                                name: '安贞门',
                                            },
                                            {
                                                id: 1012,
                                                pid: 2,
                                                name: '北土城',
                                            },
                                            {
                                                id: 1013,
                                                pid: 2,
                                                name: '立水桥',
                                            },
                                        ]
                                    },
                                    {
                                        id: 101,
                                        pid: 1,
                                        name: '长沙龙湖',
                                        children: [
                                            {
                                                id: 1012,
                                                pid: 101,
                                                name: '长沙龙湖-水利局',
                                            },
                                            {
                                                id: 10123,
                                                pid: 101,
                                                name: '长沙龙湖-水利局3',
                                            },
                                        ]
                                    }
                                ]
                            }
                        ]
                    }
                },

            }
        },
        methods: {
            getTotal() {
                function getLeafCountTree(json) {
                    if (json) {
                        if (!json.children || json.children.length == 0) {
                            json.colspan = 1;
                            return 1;
                        } else {
                            var leafCount = 0;
                            for (var i = 0; i < json.children.length; i++) {
                                leafCount = leafCount + getLeafCountTree(json.children[i]);
                            }
                            json.colspan = leafCount;
                            return leafCount;
                        }
                    }
                }

            },
            // 添加按钮
            add(fromData, toData, obj, index) {
                console.log("fromData:", fromData);
                console.log("toData:", toData);
                console.log("obj:", obj);
                console.log("Index", index)

                if (!index) {
                    this.list.one.fromData = fromData;
                    this.list.one.toData = toData
                } else {
                    this.list.two.fromData = fromData;
                    this.list.two.toData = toData
                }
            },
            // 移除按钮
            remove(fromData, toData, obj, index) {
                console.log("fromData:", fromData);
                console.log("toData:", toData);
                console.log("obj:", obj);
                if (!index) {
                    this.list.one.fromData = fromData;
                    this.list.one.toData = toData
                } else {
                    this.list.two.fromData = fromData;
                    this.list.two.toData = toData
                }
            },

            add2(fromData, toData, obj) {
                console.log("fromData:", fromData);
                console.log("toData:", toData);
                console.log("obj:", obj);
                console.log("Index", index)
                this.fromData = fromData;
                this.toData = toData
            },

            remove2(fromData, toData, obj) {
                console.log("fromData:", fromData);
                console.log("toData:", toData);
                console.log("obj:", obj);
                this.fromData = fromData;
                this.toData = toData

            },


            toggleData(index) {

                if (index === 1) {
                    this.fromData = [
                        {
                            id: 1,
                            pid: 0,
                            name: "全选",
                            children: [
                                {
                                    id: 2,
                                    pid: 1,
                                    name: "龙湖物业集团",
                                    children: []
                                },
                                {
                                    id: 3,
                                    pid: 1,
                                    name: "重庆龙湖",
                                    children: [
                                        {
                                            id: 5,
                                            pid: 3,
                                            name: "水晶郦城",
                                        },
                                        {
                                            id: 55,
                                            pid: 3,
                                            name: "水晶星座",
                                        },
                                        {
                                            id: 553,
                                            pid: 3,
                                            name: "水晶宫",
                                        }
                                    ]
                                },
                                {
                                    id: 4,
                                    pid: 1,
                                    name: "成都龙湖",
                                    children: [
                                        {
                                            id: 555,
                                            pid: 4,
                                            name: "三千龙",
                                        },
                                        {
                                            id: 6,
                                            pid: 4,
                                            name: "三千里",
                                        },
                                        {
                                            id: 655,
                                            pid: 4,
                                            name: "三千集",
                                        }
                                    ]
                                }
                            ]
                        }
                    ]
                    this.toData = [
                        {
                            id: 1,
                            pid: 0,
                            name: "全选",
                            children: [
                                {
                                    id: 2,
                                    pid: 1,
                                    name: "龙湖物业集团",
                                    children: [
                                        {
                                            id: 101,
                                            pid: 2,
                                            name: '安贞门',
                                        },
                                        {
                                            id: 1012,
                                            pid: 2,
                                            name: '北土城',
                                        },
                                        {
                                            id: 1013,
                                            pid: 2,
                                            name: '立水桥',
                                        },
                                    ]
                                },
                                {
                                    id: 101,
                                    pid: 1,
                                    name: '长沙龙湖',
                                    children: [
                                        {
                                            id: 1012,
                                            pid: 101,
                                            name: '长沙龙湖-水利局',
                                        },
                                        {
                                            id: 10123,
                                            pid: 101,
                                            name: '长沙龙湖-水利局3',
                                        },
                                    ]
                                }
                            ]
                        }
                    ]

                } else {
                    this.fromData = [
                        {
                            id: 1,
                            pid: 0,
                            name: "测试左侧",
                            children: [
                                {
                                    id: 2,
                                    pid: 1,
                                    name: "水电费是打发斯蒂芬斯蒂芬gas噶水电费噶地方死光光",
                                    children: []
                                },
                                {
                                    id: 3,
                                    pid: 1,
                                    name: "11-3",
                                    children: []
                                },
                                {
                                    id: 4,
                                    pid: 1,
                                    name: "11-4",
                                    children: [
                                        {
                                            id: 5,
                                            pid: 4,
                                            name: "11-5",
                                            children: [
                                                {
                                                    id: 111,
                                                    pid: 5,
                                                    name: "11-111",
                                                    children: []
                                                }
                                            ]
                                        },
                                        {
                                            id: 6,
                                            pid: 4,
                                            name: "11-6",
                                            children: []
                                        }
                                    ]
                                }
                            ]
                        }
                    ], // 穿梭框 - 源数据 - 树形
                        this.toData = [
                            {
                                id: 1,
                                pid: 0,
                                name: "测试左侧",
                                children: [
                                    {
                                        id: 2,
                                        pid: 1,
                                        name: "水电费是打发斯蒂芬斯蒂芬gas噶水电费噶地方死光光",
                                        children: [
                                            {
                                                id: 22222222,
                                                pid: 2,
                                                name: "2222222222",
                                                region: '1',
                                                children: []
                                            }
                                        ]
                                    }
                                ]
                            }
                        ] // 穿梭框 - 目标数据 - 树形
                }
            }
        },
    }
</script>

<style scoped>

</style>