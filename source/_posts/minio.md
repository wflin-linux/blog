---
title: minio
date: 2022-11-6
author: wflin
top: false
cover: false
toc: false
mathjax: false
summary: java 操作 oos 文件存储服务器 
tags:
  	- java
    - oos
categories:
    - oos
---

# firstOne

## MinIOUtils

```java
package com.wflin.utils;

import com.wflin.pojo.ObjectItem;
import io.minio.*;
import io.minio.messages.DeleteError;
import io.minio.messages.DeleteObject;
import io.minio.messages.Item;
import org.apache.tomcat.util.http.fileupload.IOUtils;
import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.beans.factory.annotation.Value;
import org.springframework.http.HttpHeaders;
import org.springframework.http.HttpStatus;
import org.springframework.http.MediaType;
import org.springframework.http.ResponseEntity;
import org.springframework.stereotype.Component;
import org.springframework.web.multipart.MultipartFile;

import javax.annotation.Resource;
import java.io.ByteArrayOutputStream;
import java.io.IOException;
import java.io.InputStream;
import java.io.UnsupportedEncodingException;
import java.net.URLEncoder;
import java.util.ArrayList;
import java.util.Arrays;
import java.util.List;
import java.util.stream.Collectors;

@Component
public class MinioUtil {

    @Resource
    private MinioClient minioClient;

    @Value("${minio.bucketName}")
    private String bucketName;

    /**
     * description: 判断bucket是否存在，不存在则创建
     *
     * @return: void
     */
    public void existBucket(String name) {
        try {
            boolean exists = minioClient.bucketExists(BucketExistsArgs.builder().bucket(name).build());
            if (!exists) {
                minioClient.makeBucket(MakeBucketArgs.builder().bucket(name).build());
            }
        } catch (Exception e) {
            e.printStackTrace();
        }
    }

    /**
     * 创建存储bucket
     * @param bucketName 存储bucket名称
     * @return Boolean
     */
    public Boolean makeBucket(String bucketName) {
        try {
            minioClient.makeBucket(MakeBucketArgs.builder()
                    .bucket(bucketName)
                    .build());
        } catch (Exception e) {
            e.printStackTrace();
            return false;
        }
        return true;
    }

    /**
     * 删除存储bucket
     * @param bucketName 存储bucket名称
     * @return Boolean
     */
    public Boolean removeBucket(String bucketName) {
        try {
            minioClient.removeBucket(RemoveBucketArgs.builder()
                    .bucket(bucketName)
                    .build());
        } catch (Exception e) {
            e.printStackTrace();
            return false;
        }
        return true;
    }
    /**
     * description: 上传文件
     *
     * @param multipartFile
     * @return: java.lang.String

     */
    public List<String> upload(MultipartFile[] multipartFile) {
        List<String> names = new ArrayList<>(multipartFile.length);
        for (MultipartFile file : multipartFile) {
            String fileName = file.getOriginalFilename();

            // 取文件后缀名
            String split = fileName.substring(fileName.lastIndexOf(".")+1);
            // 取文件名字并把文件种的.转为_
            String oldName = fileName.substring(0,fileName.lastIndexOf(".")).replace(".","_");
            // 新的文件名
            fileName = oldName + "_" + System.currentTimeMillis()+"."+ split;

            InputStream in = null;
            try {
                in = file.getInputStream();
                minioClient.putObject(PutObjectArgs.builder()
                        .bucket(bucketName)
                        .object(fileName)
                        .stream(in, in.available(), -1)
                        .contentType(file.getContentType())
                        .build()
                );
            } catch (Exception e) {
                e.printStackTrace();
            } finally {
                if (in != null) {
                    try {
                        in.close();
                    } catch (IOException e) {
                        e.printStackTrace();
                    }
                }
            }
            names.add(fileName);
        }
        return names;
    }

    /**
     * description: 下载文件
     *
     * @param fileName
     * @return: org.springframework.http.ResponseEntity<byte [ ]>
     */
    public ResponseEntity<byte[]> download(String fileName) {
        ResponseEntity<byte[]> responseEntity = null;
        InputStream in = null;
        ByteArrayOutputStream out = null;
        try {
            in = minioClient.getObject(GetObjectArgs.builder().bucket(bucketName).object(fileName).build());
            out = new ByteArrayOutputStream();
            IOUtils.copy(in, out);
            //封装返回值
            byte[] bytes = out.toByteArray();
            HttpHeaders headers = new HttpHeaders();
            try {
                headers.add("Content-Disposition", "attachment;filename=" + URLEncoder.encode(fileName, "UTF-8"));
            } catch (UnsupportedEncodingException e) {
                e.printStackTrace();
            }
            headers.setContentLength(bytes.length);
            headers.setContentType(MediaType.APPLICATION_OCTET_STREAM);
            headers.setAccessControlExposeHeaders(Arrays.asList("*"));
            responseEntity = new ResponseEntity<byte[]>(bytes, headers, HttpStatus.OK);
        } catch (Exception e) {
            e.printStackTrace();
        } finally {
            try {
                if (in != null) {
                    try {
                        in.close();
                    } catch (IOException e) {
                        e.printStackTrace();
                    }
                }
                if (out != null) {
                    out.close();
                }
            } catch (IOException e) {
                e.printStackTrace();
            }
        }
        return responseEntity;
    }

    /**
     * 查看文件对象
     * @param bucketName 存储bucket名称
     * @return 存储bucket内文件对象信息
     */
    public List<ObjectItem> listObjects(String bucketName) {
        Iterable<Result<Item>> results = minioClient.listObjects(
                ListObjectsArgs.builder().bucket(bucketName).build());
        List<ObjectItem> objectItems = new ArrayList<>();
        try {
            for (Result<Item> result : results) {
                Item item = result.get();
                ObjectItem objectItem = new ObjectItem();
                objectItem.setObjectName(item.objectName());
                objectItem.setSize(item.size());
                objectItems.add(objectItem);
            }
        } catch (Exception e) {
            e.printStackTrace();
            return null;
        }
        return objectItems;
    }

    /**
     * 批量删除文件对象
     * @param bucketName 存储bucket名称
     * @param objects 对象名称集合
     */
    public Iterable<Result<DeleteError>> removeObjects(String bucketName, List<String> objects) {
        List<DeleteObject> dos = objects.stream().map(e -> new DeleteObject(e)).collect(Collectors.toList());
        Iterable<Result<DeleteError>> results = minioClient.removeObjects(RemoveObjectsArgs.builder().bucket(bucketName).objects(dos).build());
        return results;
    }
}
```

## ObjectItem

```java 
@Data
public class ObjectItem {
    private String objectName;
    private Long size;
}
```



## controller

```java 
package com.wflin.controller;

import com.wflin.pojo.ObjectItem;
import lombok.extern.slf4j.Slf4j;
import org.springframework.beans.factory.annotation.Value;
import org.springframework.web.bind.annotation.*;
import org.springframework.web.multipart.MultipartFile;

import javax.annotation.Resource;
import java.util.ArrayList;
import java.util.List;

@RestController
@Slf4j
public class MinioController {

    @Resource
    private MinioUtil minioUtil;

    @Value("${minio.endpoint}")
    private String address;
    @Value("${minio.remote_endpoint}")
    private String remote_address;

    @Value("${minio.bucketName}")
    private String bucketName;

    @PostMapping("/upload")
    public Object upload(MultipartFile file) {
       
        List<String> upload = minioUtil.upload(new MultipartFile[]{file});

        // 建议不拼接网址，因为网址可能是变化的
        log.info("图片的路径 {}",remote_address+"/"+bucketName+"/"+upload.get(0));
        return remote_address+"/"+bucketName+"/"+upload.get(0);
    }

    @GetMapping("/getImages")
    public Object getImages() {
        List<ObjectItem> images = minioUtil.listObjects(bucketName);
        System.out.println(images);
        ArrayList<String> imagesList = new ArrayList<>();
        for(ObjectItem item:images){
            imagesList.add(remote_address+"/"+bucketName+"/"+item.getObjectName());
        }
        log.info("minio 中所有图片的路径 {}",imagesList);
        return imagesList;
    }
}
```

# SencondTwo

## MinIOConfig

```java
package com.wflin.config;

import io.minio.MinioClient;
import lombok.Data;
import org.springframework.beans.factory.annotation.Value;
import org.springframework.context.annotation.Bean;
import org.springframework.context.annotation.Configuration;

@Data
@Configuration
/**
 * 文件服务器配置类
 */
public class MinioConfig {

    @Value("${minio.endpoint}")
    private String endpoint;
    @Value("${minio.accessKey}")
    private String accessKey;
    @Value("${minio.secretKey}")
    private String secretKey;
    @Bean
    public MinioClient minioClient(){
        return MinioClient.builder()
                .endpoint(endpoint)
                .credentials(accessKey, secretKey)
                .build();
    }
}
```

## MinIOUitls

```java
package com.wflin.utils;

import io.minio.*;
import io.minio.http.Method;
import io.minio.messages.Bucket;
import io.minio.messages.DeleteError;
import io.minio.messages.DeleteObject;
import io.minio.messages.Item;
import org.springframework.beans.factory.annotation.Value;
import org.springframework.stereotype.Component;
import org.springframework.util.FastByteArrayOutputStream;
import org.springframework.web.multipart.MultipartFile;
import javax.annotation.Resource;
import javax.servlet.ServletOutputStream;
import javax.servlet.http.HttpServletResponse;
import java.util.ArrayList;
import java.util.List;
import java.util.stream.Collectors;

/**
 * 文件服务器工具类
 */
@Component
public class MinioUtils {

    @Resource
    private MinioClient minioClient;

    /**
     * 查看存储bucket是否存在
     * @return boolean
     */
    public Boolean bucketExists(String bucketName) {
        Boolean found;
        try {
            found = minioClient.bucketExists(BucketExistsArgs.builder().bucket(bucketName).build());
        } catch (Exception e) {
            //e.printStackTrace();
            return false;
        }
        return found;
    }

    /**
     * 创建存储bucket
     * @return Boolean
     */
    public Boolean makeBucket(String bucketName) {
        try {
            minioClient.makeBucket(MakeBucketArgs.builder()
                    .bucket(bucketName)
                    .build());
        } catch (Exception e) {
            e.printStackTrace();
            return false;
        }
        return true;
    }
    /**
     * 删除存储bucket
     * @return Boolean
     */
    public Boolean removeBucket(String bucketName) {
        try {
            minioClient.removeBucket(RemoveBucketArgs.builder()
                    .bucket(bucketName)
                    .build());
        } catch (Exception e) {
            e.printStackTrace();
            return false;
        }
        return true;
    }
    /**
     * 获取全部bucket
     */
    public List<Bucket> getAllBuckets() {
        try {
            return minioClient.listBuckets();
        } catch (Exception e) {
            e.printStackTrace();
        }
        return null;
    }

    /**
     * 文件上传
     * @param file 文件
     * @return Boolean
     */
    public String upload(MultipartFile file,String bucketName) {
        // 修饰过的文件名 非源文件名
        String fileName = "2021-07/21/";
        fileName = fileName+file.getOriginalFilename();
        try {
            PutObjectArgs objectArgs = PutObjectArgs.builder().bucket(bucketName).object(fileName)
                    .stream(file.getInputStream(),file.getSize(),-1).contentType(file.getContentType()).build();
            //文件名称相同会覆盖
            minioClient.putObject(objectArgs);

        } catch (Exception e) {
            e.printStackTrace();
            return "上传文件失败";
        }
        return bucketName+"/"+fileName;
    }

    /**
     * 预览图片
     * @param fileName
     * @return
     * @discription 如果只有一个 bucketName 可以不传入 bucketName,如果有多个，则可以传入 bucketName
     */
    public String preview(String fileName,String bucketName){
        // 查看文件地址
        GetPresignedObjectUrlArgs build = new GetPresignedObjectUrlArgs().builder().bucket(bucketName).object(fileName).method(Method.GET).build();
        try {
            String url = minioClient.getPresignedObjectUrl(build);
            return url;
        } catch (Exception e) {
            e.printStackTrace();
        }
        return null;
    }

    /**
     * 文件下载
     * @param fileName 文件名称
     * @param res response
     * @return Boolean
     */
    public void download(String fileName,String bucketName, HttpServletResponse res) {
        GetObjectArgs objectArgs = GetObjectArgs.builder().bucket(bucketName)
                .object(fileName).build();
        try (GetObjectResponse response = minioClient.getObject(objectArgs)){
            byte[] buf = new byte[1024];
            int len;
            try (FastByteArrayOutputStream os = new FastByteArrayOutputStream()){
                while ((len=response.read(buf))!=-1){
                    os.write(buf,0,len);
                }
                os.flush();
                byte[] bytes = os.toByteArray();
                res.setCharacterEncoding("utf-8");
                //设置强制下载不打开
                //res.setContentType("application/force-download");
                res.addHeader("Content-Disposition", "attachment;fileName=" + fileName);
                try (ServletOutputStream stream = res.getOutputStream()){
                    stream.write(bytes);
                    stream.flush();
                }
            }
        } catch (Exception e) {
            e.printStackTrace();
        }
    }

    /**
     * 查看文件对象
     * @return 存储bucket内文件对象信息
     */
    public List<Item> listObjects(String bucketName) {
        Iterable<Result<Item>> results = minioClient.listObjects(
                ListObjectsArgs.builder().bucket(bucketName).build());
        List<Item> items = new ArrayList<>();
        try {
            for (Result<Item> result : results) {
                items.add(result.get());
            }
        } catch (Exception e) {
            e.printStackTrace();
            return null;
        }
        return items;
    }

    /**
     * 删除
     * @param fileName
     * @return
     * @throws Exception
     */
    public boolean remove(String fileName,String bucketName){
        try {
            minioClient.removeObject( RemoveObjectArgs.builder().bucket(bucketName).object(fileName).build());
        }catch (Exception e){
            return false;
        }
        return true;
    }

    /**
     * 批量删除文件对象（没测试）
     * @param objects 对象名称集合
     */
    public Iterable<Result<DeleteError>> removeObjects(List<String> objects,String bucketName) {
        List<DeleteObject> dos = objects.stream().map(e -> new DeleteObject(e)).collect(Collectors.toList());
        Iterable<Result<DeleteError>> results = minioClient.removeObjects(RemoveObjectsArgs.builder().bucket(bucketName).objects(dos).build());
        return results;
    }

}
```

## MinIOController

```java 
package com.wflin.controller;

import com.wflin.utils.MinioUtil;
import io.minio.messages.Item;
import lombok.extern.slf4j.Slf4j;
import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.beans.factory.annotation.Value;
import org.springframework.web.bind.annotation.*;
import org.springframework.web.multipart.MultipartFile;

import javax.servlet.http.HttpServletResponse;
import java.util.List;

@RestController
@RequestMapping("/api/file/v1")
@Slf4j
public class MinioController {

    @Autowired
    private MinioUtil minioUtil;

    @Value("${minio.bucketName}")
    private String bucketName;

    @GetMapping("/bucketExists")
    public Boolean bucketExists(String bucketName) {
        System.out.println(minioUtil.bucketExists(bucketName));
        return null;
    }

    @GetMapping("/makeBucket")
    public Boolean makeBucket(String bucketName) {
        System.out.println(minioUtil.makeBucket(bucketName));
        return null;
    }

    @GetMapping("/removeBucket")
    public Boolean removeBucket(String bucketName) {
        System.out.println(minioUtil.removeBucket(bucketName));
        return null;
    }

    @GetMapping("/getAllBuckets")
    public Boolean getAllBuckets() {
        System.out.println(minioUtil.getAllBuckets());
        return null;
    }

    @GetMapping("list")
    public List getlist() {
        List<Item> itemList = minioUtil.listObjects(bucketName);
        for (Item item : itemList) {
            System.out.println(item.objectName());
        }
        return null;
    }


    @PostMapping("/upload")
    public Boolean upload(MultipartFile file) {
        System.out.println(minioUtil.upload(file,bucketName));
        return null;
    }

    @GetMapping("/preview")
    public Boolean preview(String fileName) {
        System.out.println(minioUtil.preview(fileName,bucketName));
        return null;
    }

    @GetMapping("/download")
    public Boolean download(String fileName, HttpServletResponse res) {
        minioUtil.download(fileName,bucketName,res);
        return null;
    }

    @GetMapping("/remove")
    public Boolean remove(String fileName) {
        System.out.println(minioUtil.remove(fileName,bucketName));
        return null;
    }
}
```







# three

utils

````java


import io.minio.*;
import io.minio.errors.InvalidEndpointException;
import io.minio.errors.InvalidPortException;
import io.minio.http.Method;
import io.minio.messages.Bucket;
import io.minio.messages.Item;
import org.springframework.stereotype.Component;

import java.io.InputStream;
import java.util.ArrayList;
import java.util.List;
import java.util.Map;

@Component
public class MinioUtil {

    private  MinioClient minioClient =new MinioClient("http://127.0.0.1:9000","minioadmin","minioadmin");

    public MinioUtil() throws InvalidPortException, InvalidEndpointException {
    }


    /**
	 * 创建一个桶
	 */
	public void createBucket(String bucket) throws Exception {
		boolean found = minioClient.bucketExists(bucket);
		if (!found) {
			minioClient.makeBucket(bucket);
		}
	}
	
	/**
	 * 上传一个文件
	 */
	public void uploadFile(InputStream stream, String bucket, String objectName) throws Exception {
		minioClient.putObject(bucket,objectName,stream,"application/octet-stream");
	}
	
	/**
	 * 列出所有的桶
	 */
	public List<String> listBuckets() throws Exception {
		List<Bucket> list = minioClient.listBuckets();
		List<String> names = new ArrayList<>();
		list.forEach(b -> {
			names.add(b.name());
		});
		return names;
	}
	
	/**
	 * 列出一个桶中的所有文件和目录
	 */
	public List<Fileinfo> listFiles(String bucket) throws Exception {
		Iterable<Result<Item>> results = minioClient.listObjects(bucket);
			
			List<Fileinfo> infos = new ArrayList<>();
				results.forEach(r->{
					Fileinfo info = new Fileinfo();
					try {
						Item item = r.get();
						info.setFilename(item.objectName());
						info.setDirectory(item.isDir());
						infos.add(info);
					} catch (Exception e) {
						e.printStackTrace();
					}
				});
		return infos;
	}
	
	/**
	 * 下载一个文件
	 */
	public InputStream download(String bucket, String objectName) throws Exception {
		InputStream stream = minioClient.getObject(bucket,objectName);
		return stream;
	}

	/**
	 * 生成一个文件的预览地址
	 * @return
	 */
	public String preview(String bucket, String objectName,Map<String, String> reqParams) throws Exception {
		String filePath = minioClient.getPresignedObjectUrl(Method.GET,bucket,objectName,60*60*24,reqParams);
		return filePath;
	}
	
	/**
	 * 删除一个桶
	 */
	public void deleteBucket(String bucket) throws Exception {
		minioClient.removeBucket(bucket);
	}
	
	/**
	 * 删除一个对象
	 */
	public void deleteObject(String bucket, String objectName) throws Exception {
		minioClient.removeObject(bucket,objectName);
	}
}
````



博客园

utils

````java

import com.core.utils.OptionalUtil;
import com.core.utils.StringUtil;
import com.core.utils.minio.exception.MinioException;
import io.minio.*;
import org.springframework.beans.factory.annotation.Value;
import org.springframework.stereotype.Component;

import java.io.InputStream;
import java.util.ArrayList;


/**
 * minio 文件服务器工具类
 *
 * @ClassName: MinioUtil
 */
@Component
public class MinioUtil {

    @Value("${minio.url}")
    private String url;
    @Value("${minio.port}")
    private Integer port;
    @Value("${minio.accessKey}")
    private String accessKey;
    @Value("${minio.secretKey}")
    private String secretKey;
    @Value("${minio.secure}")
    private Boolean secure;


    private MinioClient buildMinIoClient() {
        return MinioClient.builder().endpoint(url, port, secure).credentials(accessKey, secretKey).build();
    }


    /**
     * 桶是否存在
     *
     * @param bucket 桶名 @NotNull
     * @return boolean true 存在； false 不存在；
     * @operation add
     * @date 14:00 2020/10/29
     **/
    public boolean bucketExists(String bucket) {
        try {
            return buildMinIoClient().bucketExists(BucketExistsArgs.builder().bucket(bucket).build());
        } catch (Exception ex) {
            throw new MinioException(ex.getMessage(), ex);
        }
    }


    /**
     * 如果桶不存在则创建
     *
     * @param bucket 桶名 @NotNull
     * @return void
     * @operation add
     * @date 14:00 2020/10/29
     **/
    public void createBucketByNotExists(String bucket) {
        if (!bucketExists(bucket)) {
            try {
                buildMinIoClient().makeBucket(MakeBucketArgs.builder().bucket(bucket).build());
            } catch (Exception ex) {
                throw new MinioException(ex.getMessage(), ex);
            }
        }
    }


    /**
     * 流式上传文件:桶不存在会抛异常
     *
     * @param bucket 桶名称 @NotNull
     * @param objectName 存储目标名称 @NotNull 多路径存储实例： package/subpackage/.../objectName 开头不要有/
     * @param is 输入流 @NotNull
     * @param objectSize 文件大小
     * @param partSize 分片大小
     * @param contentType 文本类型 默认类型：application/octet-stream
     * @return void
     * @operation add
     * @date 14:01 2020/10/29
     **/
    public void putObjectThrows(String bucket, String objectName, InputStream is, Long objectSize, Long partSize,
        String contentType) {
        if (!bucketExists(bucket)) {
            throw new MinioException("The bucket not exists.");
        }
        putObject(bucket, objectName, is, objectSize, partSize, contentType);
    }


    /**
     * 流式上传文件:桶不存在会创建桶
     *
     * @param bucket 桶名称 @NotNull
     * @param objectName 存储目标名称 @NotNull 多路径存储实例： package/subpackage/.../objectName 开头不要有/
     * @param is 输入流 @NotNull
     * @param objectSize 文件大小
     * @param partSize 分片大小
     * @param contentType 文本类型 默认类型：application/octet-stream
     * @return void
     * @operation add
     * @date 14:01 2020/10/29
     **/
    public void putObjectSafe(String bucket, String objectName, InputStream is, Long objectSize, Long partSize,
        String contentType) {
        createBucketByNotExists(bucket);
        putObject(bucket, objectName, is, objectSize, partSize, contentType);
    }


    /**
     * 根据文件地址上传文件：桶不存在抛出异常
     *
     * @param bucket 桶名称 @NotNull
     * @param objectName 存储目标名称 @NotNull 多路径存储实例： package/subpackage/.../objectName 开头不要有/
     * @param filePath 文件存储路径 @NotNull
     * @param contentType 文本类型 默认类型：application/octet-stream
     * @return void
     * @operation add
     * @date 14:01 2020/10/29
     **/
    public void uploadObjectThrows(String bucket, String objectName, String filePath, String contentType) {
        if (!bucketExists(bucket)) {
            throw new MinioException("The bucket not exists.");
        }
        try {
            buildMinIoClient().uploadObject(UploadObjectArgs.builder()
                .bucket(bucket)
                .object(objectName)
                .filename(filePath)
                .contentType(StringUtil.convertBlankToDef(contentType, "application/octet-stream"))
                .build());
        } catch (Exception ex) {
            throw new MinioException(ex.getMessage(), ex);
        }
    }


    /**
     * 获取文件流
     *
     * @param bucket 桶名称 @NotNull
     * @param objectName 存储目标名称 @NotNull
     * @return java.io.InputStream
     * @operation add
     * @date 14:39 2020/10/29
     **/
    public InputStream getObject(String bucket, String objectName) {
        try {
            return buildMinIoClient().getObject(GetObjectArgs.builder()
                .bucket(bucket)
                .object(objectName)
                .build());
        } catch (Exception ex) {
            throw new MinioException(ex.getMessage(), ex);
        }
    }


    /**
     * 字节形式读取文件
     *
     * @param bucket 桶名称 @NotNull
     * @param objectName 存储目标名称 @NotNull
     * @return java.io.InputStream
     * @operation add
     * @date 14:39 2020/10/29
     **/
    public byte[] getObjectWithByteArray(String bucket, String objectName) {
        ArrayList<byte[]> partList = new ArrayList<>();
        try {
            InputStream is = buildMinIoClient().getObject(GetObjectArgs.builder()
                .bucket(bucket).object(objectName).build());
            byte[] buffered = new byte[1024];
            int len;
            while ((len = is.read(buffered)) > 0) {
                byte[] temp = new byte[len];
                System.arraycopy(buffered, 0, temp, 0, len);
                partList.add(temp);
            }
        } catch (Exception ex) {
            throw new MinioException(ex.getMessage(), ex);
        }
        if (partList.isEmpty()) {
            return new byte[]{0};
        }
        Integer size = partList.stream().map(x -> x.length).reduce(0, Integer::sum);
        byte[] result = new byte[size];
        int currentIndex = 0;
        for (byte[] bytes : partList) {
            for (byte aByte : bytes) {
                result[currentIndex++] = aByte;
            }
        }
        return result;
    }


    /**
     * 删除文件
     *
     * @param bucket 桶名称 @NotNull
     * @param objectName 存储目标名称 @NotNull
     * @return void
     * @operation add
     * @date 10:49 2020/10/30
     **/
    public void removeFile(String bucket, String objectName) {
        try {
            buildMinIoClient().removeObject(RemoveObjectArgs.builder().bucket(bucket).object(objectName).build());
        } catch (Exception ex) {
            throw new MinioException(ex.getMessage(), ex);
        }
    }


    /**
     * 复制文件
     *
     * @param sourceBucket 源文件桶 @NotNull
     * @param sourceObject 源文件名 @NotNull
     * @param bucket 目标桶 @NotNull
     * @param object 目标文件 @NotNull
     * @return void
     * @operation add
     * @date 11:04 2020/10/30
     **/
    public void copyObject(String sourceBucket, String sourceObject, String bucket, String object) {
        try {
            CopySource source = CopySource.builder().bucket(sourceBucket).object(sourceObject).build();
            buildMinIoClient().copyObject(CopyObjectArgs.builder().bucket(bucket).object(object).source(source).build());
        } catch (Exception ex) {
            throw new MinioException(ex.getMessage(), ex);
        }
    }


    /**
     * 流式上传文件
     *
     * @param bucket 桶名称 @NotNull
     * @param objectName 存储目标名称 @NotNull 多路径存储实例： package/subpackage/.../objectName 开头不要有/
     * @param is 输入流 @NotNull
     * @param objectSize 文件大小
     * @param partSize 分片大小
     * @return void
     * @author GYP
     * @operation add
     * @date 14:01 2020/10/29
     **/
    private void putObject(String bucket, String objectName, InputStream is, Long objectSize, Long partSize,
        String contentType) {
        try {
            objectSize = OptionalUtil.getValueOrDef(objectSize, Long.valueOf(is.available()));
            partSize = OptionalUtil.getValueOrDef(partSize, -1L);
            buildMinIoClient().putObject(PutObjectArgs.builder()
                .bucket(bucket)
                .object(objectName)
                .stream(is, objectSize, partSize)
                .contentType(StringUtil.convertBlankToDef(contentType, "application/octet-stream"))
                .build());
        } catch (Exception ex) {
            throw new MinioException(ex.getMessage(), ex);
        }
    }
}
````

