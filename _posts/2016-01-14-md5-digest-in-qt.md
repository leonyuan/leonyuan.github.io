---
layout: post
title: Generate md5 digest in Qt
---
## Function invoked

```c
QString blah = QString(QCryptographicHash::hash(("myPassword"),QCryptographicHash::Md5).toHex());
```

## Example:

```c
#include <QCoreApplication>
#include <QDebug>
#include <QString>
#include <QCryptographicHash>

int main(int argc, char *argv[])
{
    //QCoreApplication a(argc, argv);
    qDebug() << "Hello World";
    QString blah = QString(QCryptographicHash::hash(("111111"),QCryptographicHash::Md5).toHex());
    qDebug() << blah;
    //return a.exec();
}
```
