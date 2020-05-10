---
title: C# 解压zip
date: 2020-05-10 10:00
updated: 2020-05-10 10:00
tags:
- C#
---


因为项目需要我这里是打成了dll，直接上代码：
## 代码案例
```
using System;
using System.Threading;
using System.ComponentModel;
using System.IO;
using ICSharpCode.SharpZipLib.Zip;

namespace my_zip
{
    public class zip_file
    {
        static bool OverWrite = true;
        /// <summary>
        /// ZIP:解压一个zip文件 
        /// add yuangang by 2016-06-13
        /// </summary>
        /// <param name="ZipFile">需要解压的Zip文件（绝对路径）</param>
        /// <param name="TargetDirectory">解压到的目录</param>
        /// <param name="Password">解压密码</param>
        /// <param name="OverWrite">是否覆盖已存在的文件</param>
        public static void UnRarOrZip(string ZipFile, string TargetDirectory, string Password, Delegate callback = null)
        {
            //如果解压到的目录不存在，则报错
            if (!System.IO.Directory.Exists(TargetDirectory))
            {
                callback.DynamicInvoke("-1");
                throw new System.IO.FileNotFoundException("指定的目录: " + TargetDirectory + " 不存在!");
            }
            //目录结尾
            if (!TargetDirectory.EndsWith("\\")) { TargetDirectory = TargetDirectory + "\\"; }

            using (ZipInputStream zipfiles = new ZipInputStream(File.OpenRead(ZipFile)))
            {
                zipfiles.Password = Password;
                ZipEntry theEntry;

                while ((theEntry = zipfiles.GetNextEntry()) != null)
                {
                    string directoryName = "";
                    string pathToZip = "";
                    pathToZip = theEntry.Name;

                    if (pathToZip != "")
                        directoryName = Path.GetDirectoryName(pathToZip) + "\\";

                    string fileName = Path.GetFileName(pathToZip);

                    Directory.CreateDirectory(TargetDirectory + directoryName);

                    if (fileName != "")
                    {
                        if ((File.Exists(TargetDirectory + directoryName + fileName) && OverWrite) || (!File.Exists(TargetDirectory + directoryName + fileName)))
                        {
                            using (FileStream streamWriter = File.Create(TargetDirectory + directoryName + fileName))
                            {
                                int size = 2048;
                                byte[] data = new byte[2048];
                                while (true)
                                {
                                    size = zipfiles.Read(data, 0, data.Length);

                                    if (size > 0)
                                    {
                                        streamWriter.Write(data, 0, size);
                                        if ((int)((float)streamWriter.Length / (float)zipfiles.Length * 100) < 100 && callback != null)
                                            callback.DynamicInvoke(((int)((float)streamWriter.Length / (float)zipfiles.Length * 100)).ToString());
                                    }
                                    else
                                        break;
                                }
                                if (callback != null)
                                    callback.DynamicInvoke("100");
                                streamWriter.Close();
                            }
                        }
                    }
                }

                zipfiles.Close();
            }
        }
    }
}

```

