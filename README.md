# 7-Zip-JBinding-4Android
 > بسته بندی جاوا اندروید برای موتور بایگانی 7z

 ## خلاصه
 نسخه کتابخانه اندروید [7-Zip-JBinding](http://sevenzipjbind.sourceforge.net/) java wrapper.

 کتابخانه متقابل پلت فرم بومی (JNI) برای استخراج (محافظت از رمز عبور، چند قسمتی) 7z Arj BZip2 Cab Chm Cpio Deb GZip HFS Iso Lzh Lzma Nsis Rar Rpm Split Tar Udf Wim Xar Z Zip بایگانی و ایجاد 7z، Zip، Tar، GZip  & BZip2 از جاوا در اندروید.

 ### امکانات
 همه ویژگی های [7-Zip-JBinding] (http://sevenzipjbind.sourceforge.net/) پشتیبانی می شود:
 - استخراج بسیار سریع بسیاری از فرمت های آرشیو از جاوا
 - فشرده سازی 7z، Zip، Tar، GZip، BZip2
 - استخراج آرشیوهای محافظت شده با رمز عبور
 - عصاره به آرشیو مجلد تقسیم شده است
 - استخراج آرشیوهای متعدد به صورت چند رشته ای
 - 8599 تست JUnit
 - کراس پلتفرم

 ## نویسندگان
 7-Zip توسط ایگور پاولوف ([7-Zip Web Site](https://www.7-zip.org/links.html)) ایجاد شد، با 7-Zip-JBinding که در ابتدا توسط بوریس برادسکی طراحی و اجرا شد ([  7-Zip-JBinding وب سایت](http://sevenzipjbind.sourceforge.net/)).  7-Zip-JBinding توسط فردریک کلاسون برای اندروید اقتباس شده است.

 ## استفاده
 7-Zip-JBinding-4Android به دلیل تضاد نام بسته با کتابخانه 7-Zip-JBinding JAR در حال حاضر در JCenter در دسترس نیست.  با این حال، امکان وارد کردن کتابخانه AAR در Gradle از مخزن JitPack وجود دارد.
 1. مخزن JitPack را به فایل «build.gradle» سطح پروژه اضافه کنید (مثال: «MyAndroidProject/build.gradle»)
 ```gradle
allprojects {
    repositories {
        ...
        maven { url 'https://jitpack.io' }
    }
}
```
2. Add dependency to application level `build.gradle` file (example: `MyAndroidProject/app/build.gradle`)
```gradle
dependencies {
    implementation 'com.github.omicronapps:7-Zip-JBinding-4Android:Release-16.02-2.02'
}
```
3. همگام سازی پروژه
 4. SevenZip اکنون باید با وارد کردن «net.sf.sevenzipjbinding.SevenZip» امکان پذیر باشد.

 ## مثال ها
 نمونه هایی از باز کردن بایگانی موجود، همراه با فهرست کردن و استخراج مطالب در زیر ارائه شده است.

 توجه داشته باشید که کلاس `SevenZip` دسترسی ایستا را فراهم می کند و نیازی به نمونه سازی نیست.  علاوه بر این، کتابخانه بومی به طور خودکار با فراخوانی هر یک از انواع «openInArchive()» یا «getSevenZipVersion()» خواهد بود.

 ### نسخه های فهرست شده
 نسخه های 7-zip و 7-Zip-JBinding به شرح زیر قابل بازیابی هستند.  توجه داشته باشید که فراخوانی «getSevenZipVersion()» منجر به بارگیری کتابخانه بومی می‌شود و پس از آن، «isInitializedSuccessfully()» باید «true» را در صورت موفقیت برگرداند.

```java
import android.util.Log;

import net.sf.sevenzipjbinding.SevenZip;

public class TestVersion {
    private static final String TAG = "TestVersion";

    public void testVersion() {
        SevenZip.Version version = SevenZip.getSevenZipVersion();
        Log.i(TAG, "7-zip version: " + version.major + "." + version.minor + "." + version.build + " (" + version.version + "), " + version.date + version.copyright);
        Log.i(TAG, "7-Zip-JBinding version: " + SevenZip.getSevenZipJBindingVersion());
        Log.i(TAG, "Native library initialized: " + SevenZip.isInitializedSuccessfully());
    }
}
```

### فهرست مطالب آرشیو
 یک بایگانی موجود را با «openInArchive()» باز کنید، یک نمونه «IInStream» برای دسترسی به بایگانی فایل، و یک نمونه «IAarchiveOpenCallback» برای بایگانی بازخوانی‌های وضعیت باز.  در اینجا «openInArchive()» یک نمونه «IInArchive» را برمی‌گرداند که از طریق آن می‌توان بایگانی را جستجو کرد.

```java
import android.util.Log;

import net.sf.sevenzipjbinding.ArchiveFormat;
import net.sf.sevenzipjbinding.IArchiveOpenCallback;
import net.sf.sevenzipjbinding.IInArchive;
import net.sf.sevenzipjbinding.PropID;
import net.sf.sevenzipjbinding.SevenZip;
import net.sf.sevenzipjbinding.SevenZipException;
import net.sf.sevenzipjbinding.impl.RandomAccessFileInStream;

import java.io.File;
import java.io.FileNotFoundException;
import java.io.IOException;
import java.io.RandomAccessFile;

public class TestList {
    private static final String TAG = "TestList";

    public void testList(File file) {
        try {
            RandomAccessFile randomAccessFile = new RandomAccessFile(file, "r");
            RandomAccessFileInStream inStream = new RandomAccessFileInStream(randomAccessFile);
            ArchiveOpenCallback callback = new ArchiveOpenCallback();
            IInArchive inArchive = SevenZip.openInArchive(ArchiveFormat.SEVEN_ZIP, inStream, callback);

            ArchiveFormat format = inArchive.getArchiveFormat();
            Log.i(TAG, "Archive format: " + format.getMethodName());

            int itemCount = inArchive.getNumberOfItems();
            Log.i(TAG, "Items in archive: " + itemCount);
            for (int i = 0; i < itemCount; i++) {
                Log.i(TAG, "File " + i + ": " + inArchive.getStringProperty(i, PropID.PATH) + " : " + inArchive.getStringProperty(i, PropID.SIZE));
            }

            inArchive.close();
            inStream.close();
        } catch (FileNotFoundException e) {
            Log.e(TAG, e.getMessage());
        } catch (SevenZipException e) {
            Log.e(TAG, e.getMessage());
        } catch (IOException e) {
            Log.e(TAG, e.getMessage());
        }
    }

    private class ArchiveOpenCallback implements IArchiveOpenCallback {
        @Override
        public void setTotal(Long files, Long bytes) {
            Log.i(TAG, "Archive open, total work: " + files + " files, " + bytes + " bytes");
        }

        @Override
        public void setCompleted(Long files, Long bytes) {
            Log.i(TAG, "Archive open, completed: " + files + " files, " + bytes + " bytes");
        }
    }
}
```

### استخراج فایل، رابط استاندارد
 فایل ها را می توان از طریق روش «IInArchive.extract()» استخراج کرد.  این امر مستلزم ارائه یک پیاده‌سازی «IAArchiveExtractCallback» به منظور دریافت تماس‌های وضعیت و ارائه یک نمونه «ISequentialOutStream» است.  در اینجا «ISequentialOutStream» داده‌های استخراج‌شده را از طریق تماس‌های «write()» دریافت می‌کند.

```java


import android.util.Log;

import net.sf.sevenzipjbinding.ArchiveFormat;
import net.sf.sevenzipjbinding.ExtractAskMode;
import net.sf.sevenzipjbinding.ExtractOperationResult;
import net.sf.sevenzipjbinding.IArchiveExtractCallback;
import net.sf.sevenzipjbinding.IArchiveOpenCallback;
import net.sf.sevenzipjbinding.IInArchive;
import net.sf.sevenzipjbinding.ISequentialOutStream;
import net.sf.sevenzipjbinding.SevenZip;
import net.sf.sevenzipjbinding.SevenZipException;
import net.sf.sevenzipjbinding.impl.RandomAccessFileInStream;

import java.io.File;
import java.io.FileNotFoundException;
import java.io.IOException;
import java.io.RandomAccessFile;

public class TestExtract {
    private static final String TAG = "TestExtract";

    public void testExtract(File file) {
        try {
            RandomAccessFile randomAccessFile = new RandomAccessFile(file, "r");
            RandomAccessFileInStream inStream = new RandomAccessFileInStream(randomAccessFile);
            ArchiveOpenCallback callback = new ArchiveOpenCallback();
            IInArchive inArchive = SevenZip.openInArchive(ArchiveFormat.SEVEN_ZIP, inStream, callback);

            ArchiveExtractCallback extractCallback = new ArchiveExtractCallback();
            inArchive.extract(null, false, extractCallback);

            inArchive.close();
            inStream.close();
        } catch (FileNotFoundException e) {
            Log.e(TAG, e.getMessage());
        } catch (SevenZipException e) {
            Log.e(TAG, e.getMessage());
        } catch (IOException e) {
            Log.e(TAG, e.getMessage());
        }
    }


    private class ArchiveOpenCallback implements IArchiveOpenCallback {
        @Override
        public void setTotal(Long files, Long bytes) {
            Log.i(TAG, "Archive open, total work: " + files + " files, " + bytes + " bytes");
        }

        @Override
        public void setCompleted(Long files, Long bytes) {
            Log.i(TAG, "Archive open, completed: " + files + " files, " + bytes + " bytes");
        }
    }

    private class ArchiveExtractCallback implements IArchiveExtractCallback {
        @Override
        public ISequentialOutStream getStream(int index, ExtractAskMode extractAskMode) throws SevenZipException {
            Log.i(TAG, "Extract archive, get stream: " + index + " to: " + extractAskMode);
            SequentialOutStream stream = new SequentialOutStream();
            return stream;
        }

        @Override
        public void prepareOperation(ExtractAskMode extractAskMode) throws SevenZipException {
            Log.i(TAG, "Extract archive, prepare to: " + extractAskMode);
        }

        @Override
        public void setOperationResult(ExtractOperationResult extractOperationResult) throws SevenZipException {
            Log.i(TAG, "Extract archive, completed with: " + extractOperationResult);
            if (extractOperationResult != ExtractOperationResult.OK) {
                throw new SevenZipException(extractOperationResult.toString());
            }
        }

        @Override
        public void setTotal(long total) throws SevenZipException {
            Log.i(TAG, "Extract archive, work planned: " + total);
        }

        @Override
        public void setCompleted(long complete) throws SevenZipException {
            Log.i(TAG, "Extract archive, work completed: " + complete);
        }
    }

    private class SequentialOutStream implements ISequentialOutStream {
        @Override
        public int write(byte[] data) throws SevenZipException {
            if (data == null || data.length == 0) {
                throw new SevenZipException("null data");
            }
            Log.i(TAG, "Data to write: " + data.length);
            return data.length;
        }
    }
}
```

### استخراج آهسته، رابط استاندارد
 از طرف دیگر، رابط استخراج کند را می توان از طریق «IInArchive.extractSlow()» استفاده کرد، که با این حال نیاز به دسترسی به هر فایل جداگانه دارد.

```java
import android.util.Log;

import net.sf.sevenzipjbinding.ArchiveFormat;
import net.sf.sevenzipjbinding.ExtractOperationResult;
import net.sf.sevenzipjbinding.IArchiveOpenCallback;
import net.sf.sevenzipjbinding.IInArchive;
import net.sf.sevenzipjbinding.ISequentialOutStream;
import net.sf.sevenzipjbinding.SevenZip;
import net.sf.sevenzipjbinding.SevenZipException;
import net.sf.sevenzipjbinding.impl.RandomAccessFileInStream;

import java.io.File;
import java.io.FileNotFoundException;
import java.io.IOException;
import java.io.RandomAccessFile;

public class TestSlowExtract {
    private static final String TAG = "TestSlowExtract";

    public void testSlowExtract(File file) {
        try {
            RandomAccessFile randomAccessFile = new RandomAccessFile(file, "r");
            RandomAccessFileInStream inStream = new RandomAccessFileInStream(randomAccessFile);
            ArchiveOpenCallback callback = new ArchiveOpenCallback();
            IInArchive inArchive = SevenZip.openInArchive(ArchiveFormat.SEVEN_ZIP, inStream, callback);

            int itemCount = inArchive.getNumberOfItems();
            SequentialOutStream outStream = new SequentialOutStream();
            for (int i = 0; i < itemCount; i++) {
                ExtractOperationResult result = inArchive.extractSlow(i, outStream);
                if (result != ExtractOperationResult.OK) {
                    Log.e(TAG, result.toString());
                }
            }

            inArchive.close();
            inStream.close();
        } catch (FileNotFoundException e) {
            Log.e(TAG, e.getMessage());
        } catch (SevenZipException e) {
            Log.e(TAG, e.getMessage());
        } catch (IOException e) {
            Log.e(TAG, e.getMessage());
        }
    }

    private class ArchiveOpenCallback implements IArchiveOpenCallback {
        @Override
        public void setTotal(Long files, Long bytes) {
            Log.i(TAG, "Archive open, total work: " + files + " files, " + bytes + " bytes");
        }

        @Override
        public void setCompleted(Long files, Long bytes) {
            Log.i(TAG, "Archive open, completed: " + files + " files, " + bytes + " bytes");
        }
    }

    private class SequentialOutStream implements ISequentialOutStream {
        @Override
        public int write(byte[] data) throws SevenZipException {
            if (data == null || data.length == 0) {
                throw new SevenZipException("null data");
            }
            Log.i(TAG, "Data to write: " + data.length);
            return data.length;
        }
    }
}
```

## یادداشت های انتشار
 ویژگی های اصلی 16.02-2.02 (انتشار، کراس پلتفرم، بر اساس zip/p7zip 16.02)
 * رفع اشکال شماره 5 RandomAccessFileOutStream باید Closeable را پیاده سازی کند
   - (https://github.com/borisbrodski/sevenzipjbinding/issues/5)

 ویژگی های اصلی 16.02-2.01 (انتشار، استخراج/فشرده سازی/به روز رسانی، کراس پلتفرم، بر اساس zip/p7zip 16.02)
 * Bind 7-Zip 16.02، استخراج/ایجاد/به روز رسانی بایگانی در حافظه
 * استخراج از
   - 7z، Arj، BZip2، Cab، Chm، Cpio، Ar/A/Lib/Deb، Fat، GZip، HFS، Iso،
     Lzh، Lzma، Nsis، Ntfs، Rar، RPM، Split، Tar، Udf، Wim، Xar، Z، Zip
   - تشخیص خودکار فرمت آرشیو
   - پشتیبانی از رمز عبور محافظت شده و در حجم آرشیو تقسیم شده است
   - رابط های استخراج کامل و ساده شده
 * فشرده سازی و به روز رسانی
   - 7z، Zip، Tar، GZip، BZip2
   - فرمت آرشیو مناسب APIهای فشرده سازی خاص و عمومی
   - آرشیوهای محافظت شده با رمز عبور (7z، zip) با هدرهای آرشیو رمزگذاری شده (فقط 7z)
 * JavaDoc + Snippets (به مستندات در وب مراجعه کنید: sevenzipjbind.sf.net)
 * بیش از 8599 تست JUnit:
   - مقداردهی اولیه
   - کلیه روش های استخراج
   - فشرده سازی
   - پشتیبانی از یونیکد

 ویژگی های اصلی نسخه 9.20-2.00 بتا (نامزد انتشار، استخراج/فشرده سازی/به روز رسانی، کراس پلتفرم، بر اساس zip/p7zip 9.20)
 * استخراج از
   - 7z، Arj، BZip2، Cab، Chm، Cpio، Deb، GZip، HFS، Iso، Lzh،
     Lzma، Nsis، Rar، RPM، Split، Tar، Udf، Wim، Xar، Z، Zip
   - تشخیص خودکار فرمت آرشیو
   - پشتیبانی از آرشیوهای محافظت شده با رمز عبور و حجم
   - رابط استخراج ساده
 * فشرده سازی و به روز رسانی
   - 7z، Zip، Tar، GZip، BZip2
   - API فشرده سازی خاص یا عمومی با فرمت آرشیو
 * JavaDoc + Snippets (به مستندات موجود در وب مراجعه کنید: [sevenzipjbind.sf.net](http://sevenzipjbind.sf.net))
 * 6766 تست JUnit:
   - 7z، Zip، Tar، Rar، Lzma، Iso، GZip، Cpio، BZIP2،
     Z، Arj، Lzh، کابین، Chm، Nsis، DEB، RPM، UDF

 ## مجوز
 [کتابخانه گنو یا مجوز عمومی عمومی کمتر نسخه 2.1 (LGPLv2)] (LICENSE)


##ترجمه

- ترجمه شده توسط نینجا رمزگشا
