swagger: "2.0"
info:
  description: "koyu-CMS-API Services Documents."
  version: "1.0.0"
  title: "koyu-CMS-API Services Documents"
  contact:
    email: "max_liuhu@163.com"
host: "192.168.3.19"
basePath: "/v2"
tags:
- name: "assignments"
  description: "资源分配相关操作"
- name: "virtualmachines"
  description: "虚拟机管理的相关操作"
- name: "terminals"
  description: "终端管理相关操作"
- name: "templates"
  description: "模板管理"
- name: "containers"  
  description: "容器管理"

schemes:
- "http"
- 'https'

paths:
  /assignments:
    get:
      summary: 获取现有资源列表
      description: 获取当前所有资源列表，包含相关信息，同时可按照要进行模糊匹配查询
      responses:
        200:
          description: OK
          schema:
            type: array
            items:
              required: [owners,name,modify_url]
            properties:
              owners: {description: '所有者', type: string}
              name: {description: '名称', type: string}
              modify_url: {description: '资源', type: string}
      parameters:
       - {name: name, required: false, in: query, description: '名称，按名称进行模配查找', type: string}
       - {name: limit, required: true, in: query, description: 'pagesize', type: integer}
       - {name: marker, required: true, in: query, description: '上一页', type: integer}
      tags: [assignments]

    post:
      summary: 为用户分配新的资源
      description: 为用户分配新的资源
      responses:
        201:
          description: CREATED
          schema:
            type: array
            items:
              required: [owners,name,modify_url]
            properties:
              csrf_token: {description: '', type: string}
              users: {description: '所有者', type: integer}
              name: {description: '名称', type: string}
              vms: {description: '', type: integer}
              vmsearch: {description: '', type: string}
        409:
          description: CONFLICT

      parameters:
        - {name: name,required: true,in: query,description: '新资源',type: string}
      consumes: [application/json]
      tags: [assignments]

  /assignments/{id}:
    put:
      summary: 根据id修改指定用户分配的资源
      description: 修改指定用户分配的资源,一次只能修改一个id
      responses:
        202:
          description: ACCEPTED
          schema:
            type: object
            items:
                required: [owners,name,modify_url]
            properties:
              csrf_token: {description: '', type: string}
              owners: {description: '所有者', type: string}
              name: {description: '名称', type: string}
              modify_url: {description: '资源', type: string}
              id: {description: '', type: integer}
              resources: {description: '资源', type: string}
              selectable: {description: '', type:  boolean}
      parameters:
        - {name: id, required: true, in: path, description: '', type: integer}
        - {name: name, required: true, in: query , description: '名称', type: string}  
        - {name: owners, required: true, in: query , description: '名称', type: string}
        - {name: resources, required: true, in: query , description: '名称', type: string}
        - {name: limit, required: true, in: query, description: 'pagesize', type: integer}
        - {name: marker, required: true, in: query, description: '上一页', type: integer}


      tags: [assignments]


    delete:
      summary:  删除指定用户所分配资源
      description: 删除指定用户所分配资源
      responses:
        204:
          description: NO CONTENT
      parameters:
      - {name: id,required: true, in: path, description: '', type: integer}
      - {name: limit, required: true, in: query, description: 'pagesize', type: integer}
      - {name: marker, required: true, in: query, description: '上一页', type: integer}
      tags: [assignments]


  /virtualmachines:
    get:
      summary: 获取虚拟机信息列表
      description: 获取虚拟机信息列表,含多种属性与状态，可以进行模糊配皮查询
      responses:
        200:
          description: OK
          schema:
            type: array
            items:
              required: [os_name,template,agent_status,modify_url]
            properties:
              os_name: {description: '', type: string}
              template: {description: '', type: string}
              agent_status: {description: '',type: integer}
              modify_url: {description: '', type: string}

      parameters:
        - {name: host_server_id, required: false, in: query, description: '根据服务器进行删选', type: integer}
        - {name: power_state, required: false, in: query, description: '根据电源状态进行删选', type: integer}
        - {name: agent_status, required: false, in: query, description: '根据agent状态进行删选', type: integer}
        - {name: uuid, required: false, in: query, description: 'uuid', type: integer}
        - {name: limit, required: true, in: query, description: 'pagesize', type: integer}
        - {name: marker, required: true, in: query, description: '上一页', type: integer}
      tags: [virtualmachines]

  /virtualmachines/{id}:
    put:
      summary: 根据选择结构，修改对应的模板名称
      description: 修改虚拟机的模板
      responses:
        200:
          description: OK
          schema:
            type: array
            items:
              required: [template_id]
            properties:
              name: {description: '', type: string}
              template_id: {description: '', type: integer}
              id: {description: '',type: integer}
              crsf_token: {description: '', type: string}
        404:
          description: NOT FOUND

      parameters:
        - {name: id, required: true, in: path, description: '根据服务器进行删选', type: integer}
        - {name: limit, required: true, in: query, description: 'pagesize', type: integer}
        - {name: marker, required: true, in: query, description: '上一页', type: integer}
      tags: [virtualmachines]

  /virtualmachines/push_package:
    put:
      summary: 推送包
      description: 推送包
      responses:
        200:
          description: OK
      parameters:
        - {name: push_package, required: true, in: path, description: '', type: integer}
      tags: [virtualmachines]

  /virtualmachines/push_userfile:
    put:
      summary: 分发文件
      description: 分发文件
      responses:
        200:
          description: OK
      parameters:
        - {name: push_userfile, required: true, in: path, description: '', type: integer}
      tags: [virtualmachines]    


  /terminals:
    get:
      summary: 获取终端设备相关信息列表
      description: 获取终端设备相关信息列表
      responses:
        200:
          description: OK
          schema:
            type: array
            items:
              required: [status,alias,id,name,mpdify_url,connnectivity,vdi_version,device_manager,ipv4,selectable]
            properties:
              status: {description: '', type: string}
              alias: {description: '', type: string }
              id: {description: '', type: integer}
              name: {description: '', type: string}
              modify_url: {description: '', type: string}
              connectivity: {description: '', type: string}
              vdi_version: {description: '', type: string}
              device_manager: {description: '', type: string}
              ipv4: {description: '', type: string}
              selectable:  {description: '', type: boolean}
      parameters:
      - {name: host_server_id, required: false, in: query, description: '通过虚拟化服务器查找', type: integer}
      - {name: name, required: false, in: query, description: '通过名称查找', type: integer}
      - {name: limit, required: true, in: path, description: 'pagesize', type: integer}
      - {name: marker, required: true, in: path, description: '上一页', type: integer}

      tags: [terminals]

  /terminals/{id}:
    put:
      summary: 修改指定终端设备的别名
      description: 通过id修改指定终端设备的别名
      responses:
        202:
          description: ACCEPTED
          schema:
            type: object
            items:
              required: [alias]
              # [status,alias,id,name,mpdify_url,connnectivity,vdi_version,device_manager,ipv4,selectable]
            properties:
              status: {description: '', type: string}
              alias: {description: '', type: string }
              id: {description: '', type: integer}
              name: {description: '', type: string}
              modify_url: {description: '', type: string}
              connectivity: {description: '', type: string}
              vdi_version: {description: '', type: string}
              device_manager: {description: '', type: string}
              ipv4: {description: '', type: string}
              selectable:  {description: '', type: boolean}
      parameters:
      - {name: id, required: true, in: path, description: '终端设备id', type: integer}
      tags: [terminals]

    delete:
      summary: 删除指定终端
      description: 通过id查找，然后删除指定终端
      responses:
        204:
          description: NO CONTENT
      parameters:
      - {name: id,required: true, in: path, description: '', type: integer}
      tags: [terminals]

  /terminals/push_package:  
    post:
      summary: 向选中的终端设备推送包
      description: 向选中的终端设备推送包
      tags: [terminals]
      responses:
        200:
          description: OK
          schema:
            type: object
            items:
              required: [status,alias,id,name,mpdify_url,connnectivity,vdi_version,device_manager,ipv4,selectable]
            properties:
              status: {description: '', type: string}
              alias: {description: '', type: string }
              id: {description: '', type: integer}
              name: {description: '', type: string}
              modify_url: {description: '', type: string}
              connectivity: {description: '', type: string}
              vdi_version: {description: '', type: string}
              device_manager: {description: '', type: string}
              ipv4: {description: '', type: string}
              selectable:  {description: '', type: boolean}
        500:
          description: INTERNAL SERVER ERROR
      parameters:
      - {name: push_package, required: true, in: path, description: '推送指定包', type: string}


  /templates:
    get:
      summary: 查找模板
      description: 查找模板,通过名称与UUID模糊查询，可以查询集合或者的单个
      tags: [templates]
      responses:
        200:
          description: OK
          schema:
            type: array
            items:
              required: [status,alias,id,name,mpdify_url,connnectivity,vdi_version,device_manager,ipv4,selectable]
            properties:
              name: {description: '', type: string}
              uuid: {description: '', type: string}
              activity: {description: '', type: string}
              selectable:  {description: '', type: boolean}
              host_server: {description: '', type: string }
              id: {description: '', type: integer}

      parameters:
      - {name: id, required: true, in: path, description: '用户ID', type: integer}
      - {name: name, required: true, in: query, description: '模板名称', type: string}
      - {name: uuid, required: true, in: query, description: '模板对应的uuid', type: string}
      - {name: limit, required: true, in: query, description: 'pagesize', type: integer}
      - {name: marker, required: true, in: query, description: '上一页', type: integer}

  /templates/{id}:  
    delete:
      summary:  删除指定模板
      description: 删除选中的模板
      tags: [templates]
      responses:
        204:
          description: NO CONTENT
      parameters:
      - {name: id,required: true, in: path, description: '', type: integer}

  /templates/clone/{id}:    
    post:
      summary:  从指定的模板创建
      description: 从指定的模板创建
      tags: [templates]
      responses:
        200:
          description: OK
      parameters:
      - {name: id, required: true, in: path, description: '', type: integer}
      - {name: csrf_token, required: true, in: header, description: '', type: integer}
      - {name: vm_name, required: true, in: header , description: '', type: integer}
      - {name: vm_number, required: true, in: header, description: '', type: integer}


  /containers:
    get:
        summary: 获取容器信息列表
        description: 获取容器信息列表
        tags: [containers]
        responses:
          200:
            description: OK
        parameters:
        - {name: name, required: true, in: query, description: '容器名称', type: string}
        - {name: uuid, required: true, in: query, description: '对应的uuid', type: string}
        - {name: limit, required: true, in: query, description: 'pagesize', type: integer}
        - {name: marker, required: true, in: query, description: '上一页', type: integer}
    
