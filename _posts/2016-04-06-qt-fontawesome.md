---
layout: post
title:  "[Qt] Use fontawesome in qt."
date:   2016-04-06
comments: true
categories: Qt
tags: [c++,qt]
description: "Use fontawesome in qt"
published: true
---

fontawesome-webfont.ttf can be download in <a href="http://www.bootcss.com/p/font-awesome/" target="_blank">font-awesome</a>

<strong>iconhelper.h</strong>

```cpp
#ifndef ICONHELPER_H
#define ICONHELPER_H

#include <QObject>
#include <QFont>
#include <QFontDatabase>
#include <QMutex>
#include <QLabel>
#include <QPushButton>
#include <QApplication>
#include <QTreeWidgetItem>

#define DEFAULT_ICON_SIZE 10
#define ICON_SIZE 12

class IconHelper : public QObject
{
public:
    static IconHelper* Instance()
    {
        static QMutex mutex;
        if(!_instance)
        {
            QMutexLocker locker(&mutex);
            if(!_instance)
            {
                _instance = new IconHelper;
            }
        }
        return _instance;
    }

    void SetIcon(QLabel* lab, QChar c, int size = DEFAULT_ICON_SIZE);
    void SetIcon(QPushButton* btn, QChar c, int size = DEFAULT_ICON_SIZE);

private:
    explicit IconHelper(QObject *parent = 0);
    QFont iconFont;
    static IconHelper* _instance;
};

#endif // ICONHELPER_H
```
<strong>iconhelper.cpp</strong>

```cpp
#include "iconhelper.h"

IconHelper* IconHelper::_instance = 0;
IconHelper::IconHelper(QObject *) :
    QObject(qApp)
{
    int fontId = QFontDatabase::addApplicationFont(":/image/fontawesome/fontawesome-webfont.ttf");
    QString fontName = QFontDatabase::applicationFontFamilies(fontId).at(0);
    iconFont = QFont(fontName);
}

void IconHelper::SetIcon(QLabel *lab, QChar c, int size)
{
    iconFont.setPointSize(size);
    lab->setFont(iconFont);
    lab->setText(c);
}

void IconHelper::SetIcon(QPushButton *btn, QChar c, int size)
{
    iconFont.setPointSize(size);
    btn->setFont(iconFont);
    btn->setText(c);
}
```
