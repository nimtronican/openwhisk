---

copyright:
  years: 2016, 2018
lastupdated: "2018-07-31"

---

{:shortdesc: .shortdesc}
{:codeblock: .codeblock}
{:screen: .screen}
{:pre: .pre}
{:tip: .tip}

# Object Storage 包
{: #cloud_object_storage_actions}

{{site.data.keyword.cos_full_notm}} 包提供了一组用于与 {{site.data.keyword.cos_full}} 实例进行交互的操作。这些操作允许您在 {{site.data.keyword.cos_short}} 实例上存在的存储区中执行读取、写入和删除操作。
{: shortdesc}

{{site.data.keyword.cos_short}} 包中包含以下操作：

|实体|类型|参数|描述
|
| --- | --- | --- | --- |
|`/cloud-object-storage`|包|apikey、resource_instance_id、cos_hmac_keys.access_key_id、cos_hmac_keys.secret_access_key|使用 {{site.data.keyword.cos_full_notm}} 实例。|
|`/cloud-object-storage/object-write`|操作|bucket、key、body|将对象写入存储区。|
|`/cloud-object-storage/object-read`|操作|bucket、key|从存储区读取对象。|
|`/cloud-object-storage/object-delete`|操作|bucket、key|从存储区中删除对象。|
|`/cloud-object-storage/bucket-cors-put`|操作|bucket、corsConfig|将 CORS 配置分配给存储区。|
|`/cloud-object-storage/sucket-cors-get`|操作|bucket|读取存储区的 CORS 配置。|
|`/cloud-object-storage/sucket-cors-delete`|操作|bucket|删除存储区的 CORS 配置。|
|`/cloud-object-storage/client-get-signed-url`|操作|bucket、key、operation|获取签名的 URL 以限制对存储区中对象的写入、读取和删除操作。|

## 创建 {{site.data.keyword.cos_full_notm}} 服务实例
{: #cloud_object_storage_service_instance}

安装包之前，必须请求 {{site.data.keyword.cos_short}} 的实例，并至少创建一个存储区。

1. [创建 {{site.data.keyword.cos_full_notm}} 服务实例 ![外部链接图标](../icons/launch-glyph.svg "外部链接图标")](/docs/services/cloud-object-storage/basics/order-storage.html#creating-a-new-service-instance)。

2. 为 {{site.data.keyword.cos_short}} 服务实例[创建一组 HMAC 服务凭证 ![外部链接图标](../icons/launch-glyph.svg "外部链接图标")](/docs/services/cloud-object-storage/iam/service-credentials.html#service-credentials)。在**添加内联配置参数（可选）**字段中，添加 `{"HMAC":true}`。

3. [至少创建一个存储区 ![外部链接图标](../icons/launch-glyph.svg "外部链接图标")](/docs/services/cloud-object-storage/getting-started.html#create-buckets)。

## 安装 {{site.data.keyword.cos_short}} 包
{: #cloud_object_storage_installation}

具有 {{site.data.keyword.cos_short}} 服务实例后，请使用 {{site.data.keyword.openwhisk}} CLI 或 UI 将 {{site.data.keyword.cos_short}} 包安装到名称空间中。
{: shortdesc}

### 通过 {{site.data.keyword.openwhisk_short}} CLI 进行安装
{: #cloud_object_storage_cli}

开始之前：
  1. [安装 {{site.data.keyword.Bluemix_notm}} CLI 的 {{site.data.keyword.openwhisk_short}} 插件](bluemix_cli.html#cloudfunctions_cli)。
  2. 安装 `wskdeploy` 命令。请参阅 [Apache OpenWhisk 文档 ![外部链接图标](../icons/launch-glyph.svg "外部链接图标")](https://github.com/apache/incubator-openwhisk-wskdeploy#building-the-project)。

要安装 {{site.data.keyword.cos_short}} 包，请执行以下操作：

1. 克隆 {{site.data.keyword.cos_short}} 包存储库。
    ```
    git clone https://github.com/ibm-functions/package-cloud-object-storage.git
    ```
    {: pre}

2. 浏览至 `runtimes/nodejs` 或 `runtimes/python` 目录。{{site.data.keyword.cos_short}} 包中的操作会部署在您选择的运行时中。
    ```
    cd package-cloud-object-storage/runtimes/nodejs
    ```
    {: pre}

3. 部署包。如果您日后决定在其他运行时中运行此包中的操作，那么可以重复上一步，然后执行此步骤以重新部署此包。
    ```
    wskdeploy -m manifest.yaml
    ```
    {: pre}

4. 验证 `cloud-object-storage` 包是否已添加到包列表中。
    ```
    ibmcloud fn package list
    ```
    {: pre}

    输出：
    ```
    packages
    /myOrg_mySpace/cloud-object-storage-pkg private
    ```
    {: screen}

5. 将所创建的 {{site.data.keyword.cos_short}} 实例中的凭证绑定到包。
    ```
    ibmcloud fn service bind cloud-object-storage cloud-object-storage-pkg
    ```
    {: pre}

    示例输出：
    ```
    Credentials 'Credentials-1' from 'cloud-object-storage' service instance 'Cloud Object Storage-r1' bound to 'cloud-object-storage-pkg'.
    ```
    {: screen}

6. 验证该包是否已配置为使用 {{site.data.keyword.cos_short}} 服务实例凭证。
    ```
    ibmcloud fn package get /myBluemixOrg_myBluemixSpace/cloud-object-storage-pkg parameters
    ```
    {: pre}

    示例输出：
    ```
    ok: got package /myBluemixOrg_myBluemixSpace/cloud-object-storage-pkg, displaying field parameters
    [
      {
        "key": "__bx_creds",
            "value": {
                "cloud-object-storage": {
            "apikey": "sdabac98wefuhw23erbsdufwdf7ugw",
            "credentials": "Credentials-1",
            "endpoints": "https://cos-service-s.us-south.containers.mybluemix.net/endpoints",
            "iam_apikey_description": "Auto generated apikey during resource-key operation for Instance - crn:v1:staging:public:cloud-object-storage:global:a/ddkgdaf89uawefoujhasdf:sd8238-sdfhwej33-234234-23423-213d::",
            "iam_apikey_name": "auto-generated-apikey-sduoiw98wefuhw23erbsdufwdf7ugw",
            "iam_role_crn": "crn:v1:bluemix:public:iam::::serviceRole:Reader",
            "iam_serviceid_crn": "crn:v1:staging:public:iam-identity::a/dd166ddkjadf89uawefoujhasdf3bc1f8e4d6818b8da577757528e::serviceid:ServiceId-sd8238-sdfhwej33-234234-23423-213d",
            "instance": "Cloud Object Storage-r1",
            "resource_instance_id": "crn:v1:staging:public:cloud-object-storage:global:a/dd166ddkjadf89uawefoujhasdf3bc1f8e4d6818b8da577757528e:sd8238-sdfhwej33-234234-23423-213d::"
          }
         }
      }
    ]
    ```
    {: screen}

### 通过 {{site.data.keyword.openwhisk_short}} UI 进行安装
{: #cloud_object_storage_ui}

1. 在 {{site.data.keyword.openwhisk_short}} 控制台中，转至[“创建”页面 ![外部链接图标](../icons/launch-glyph.svg "外部链接图标")](https://console.bluemix.net/openwhisk/create)。

2. 使用 **Cloud Foundry 组织**和 **Cloud Foundry 空间**列表，选择要将 {{site.data.keyword.cos_short}} 包安装到其中的名称空间。名称空间由组合的组织和空间名称构成。

3. 单击**安装包**。

4. 单击 **IBM Cloud Object Storage** 包组，然后单击 **IBM Cloud Object Storage** 包。

5. 在“可用运行时”部分中，从下拉列表中选择 NodeJS 或 Python，然后单击**安装**。

6. 安装包后，会将您重定向到“操作”页面，您可以在其中搜索名为 **cloud-object-storage** 的新包。

7. 要使用 **cloud-object-storage** 包中的操作，必须将服务凭证绑定到操作。
  * 要将服务凭证绑定到包中的所有操作，请遵循上面列出的 CLI 指示信息中的步骤 5 和 6。
  * 要将服务凭证绑定到单个操作，请在 UI 中完成以下步骤。**注**：对于要使用的每个操作，必须完成以下步骤。
    1. 单击 **cloud-object-storage** 包中要使用的操作。这将打开该操作的详细信息页面。
    2. 在左侧导航中，单击**参数**部分。
    3. 输入新的**参数**。对于键，输入 `__bx_creds`。对于值，请从先前创建的服务实例中粘贴服务凭证 JSON 对象。

## 写入 {{site.data.keyword.cos_short}} 存储区
{: #cloud_object_storage_write}

可以使用 `object-write` 操作将对象写入 {{site.data.keyword.cos_short}} 存储区。
{: shortdesc}

**注**：在以下步骤中，名称 `testbucket` 用作示例。{{site.data.keyword.cos_full_notm}} 中的存储区必须全局唯一，因此必须将 `testbucket` 替换为唯一的存储区名称。

### 通过 CLI 写入存储区
{: #write_bucket_cli}

使用 `object-write` 操作将对象写入存储区。
```
ibmcloud fn action invoke /_/cloud-object-storage/object-write --blocking --result --param bucket testbucket --param key data.txt --param body "my_test_data"
```
{: pre}

示例输出：
```
{
  "body": {
      "ETag": "\"32cef9b573122b1cf8fd9aec5fdb898c\""
  },
  "bucket": "testbucket",
  "key": "data.txt"
}
```
{: screen}

### 通过 UI 写入存储区
{: #write_bucket_ui}

1. 转至 [{{site.data.keyword.openwhisk_short}} 控制台中的“操作”页面 ![外部链接图标](../icons/launch-glyph.svg "外部链接图标")](https://console.bluemix.net/openwhisk/actions)。

2. 在 `cloud-object-storage` 包下，单击 **object-write** 操作。

3. 在“代码”窗格中，单击**更改输入**。

4. 输入包含您的存储区、键和主体的 JSON 对象作为对象键。
    ```
    {
      "bucket": "testbucket",
      "key": "data.txt",
      "body": "my_test_data"
    }
    ```
    {: pre}

5. 单击**保存**。

6. 单击**调用**。

7. 验证输出是否类似于以下内容：
    ```
    object-write 3855 ms 6/7/2018, 14:56:09
    Activation ID: bb6eba3cf69wereaeba3cf691a1aad8
    Results:
    {
      "bucket": "testbucket",
      "key": "data.txt",
      "body": {
        "ETag": "\"af1c16ea127ab52040d86472c45978fd\""
      }
    }
    Logs:
    []
    ```
    {: screen}

## 从 {{site.data.keyword.cos_short}} 存储区进行读取
{: #cloud_object_storage_read}

可以使用 `object-read` 操作读取 {{site.data.keyword.cos_short}} 存储区中的对象。
{: shortdesc}

**注**：在以下步骤中，名称 `testbucket` 用作示例。{{site.data.keyword.cos_full_notm}} 中的存储区必须全局唯一，因此必须将 `testbucket` 替换为唯一的存储区名称。

### 通过 CLI 从存储区进行读取
{: #read_bucket_cli}

使用 `object-read` 操作读取存储区中的对象。
```
ibmcloud fn action invoke /_/cloud-object-storage/object-read --blocking --result --param bucket testbucket --param key data.txt
```
{: pre}

示例输出：
```
{
  "body": "my_test_data",
  "bucket": "testbucket,
  "key": "data.txt"
}
```
{: screen}

### 通过 UI 从存储区进行读取
{: #read_bucket_ui}

1. 转至 [{{site.data.keyword.openwhisk_short}} 控制台中的“操作”页面 ![外部链接图标](../icons/launch-glyph.svg "外部链接图标")](https://console.bluemix.net/openwhisk/actions)。

2. 在 `cloud-object-storage` 包下，单击 **object-read** 操作。

3. 在“代码”窗格中，单击**更改输入**。

4. 输入包含您的存储区和键的 JSON 对象作为对象键。
    ```
    {
      "bucket": "testbucket",
      "key": "data.txt",
    }
    ```
    {: pre}

5. 单击**保存**。

6. 单击**调用**。

7. 验证输出是否类似于以下内容：   
    ```
    object-write 3855 ms 6/7/2018, 14:56:09
    Activation ID: bb6eba3cf69wereaeba3cf691a1aad8
    Results:
    {
      "bucket": "testbucketeweit",
      "key": "data.txt",
      "body": {
        "ETag": "\"af1c16ea127ab52040d86472c45978fd\""
      }
    }
    Logs:
    []
    ```
    {: screen}
