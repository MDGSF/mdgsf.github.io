---
layout: post
title:  "[Qt] checkbox."
date:   2016-04-06
comments: true
categories: Qt
tags: [c++,qt]
description: ""
published: true
---

<strong>checkbox.h</strong>

```cpp
#ifndef CCHECKBOX_H
#define CCHECKBOX_H

#include <QWidget>

class QPushButton;
class QLabel;

enum ECheckBoxState {
    ENoChecked,
    EChecked
};

class CCheckBox : public QWidget
{
    Q_OBJECT
public:
    explicit CCheckBox(QString str,
        int iWidth,
        int iHeight,
        QWidget *parent = 0);
    void setChecked(bool b);
    bool isChecked();

signals:
    void sigStateChanged(int iState);

private slots:
    void slot_btn_clicked(bool checked);

protected:
    bool eventFilter(QObject *obj, QEvent *event);

private:
    QPushButton * m_pBtn;
    QLabel * m_pLabel;
    int m_iCurState;

    QString m_strStyle;
    QString m_strHoverStyle;
    QString m_strPressedStyle;
};

#endif // CCHECKBOX_H
```


<strong>checkbox.cpp</strong>

```cpp
#include "checkbox.h"
#include <QPushButton>
#include <QLabel>
#include <QHBoxLayout>
#include "../common/iconhelper.h"

CCheckBox::CCheckBox(
                     QString str,
                     int iWidth,
                     int iHeight,
                     QWidget *parent) :
    QWidget(parent)
{
    m_pBtn = new QPushButton(this);
    m_pLabel = new QLabel(this);

    m_pBtn->setMinimumSize(iWidth, iHeight);
    m_pBtn->setMaximumSize(iWidth, iHeight);
    m_pBtn->setFlat(true);
    m_pBtn->setCheckable(true);
    m_pBtn->installEventFilter(this);

    m_pLabel->setText(str);
    m_pLabel->installEventFilter(this);

    QHBoxLayout *layout = new QHBoxLayout();
    layout->setContentsMargins(0, 0, 0, 0);
    layout->addWidget(m_pBtn);
    layout->addWidget(m_pLabel);
    this->setLayout(layout);


    m_strStyle = "QPushButton{border: 1px solid #B2B2B2; border-radius: 0; color: white; background-color: white;}"
                 "QPushButton:hover{border: 1px solid #278CDE; background-color: white;}"
                 "QPushButton:checked{}";

    m_strHoverStyle =
            "QPushButton{border: 1px solid #278CDE; border-radius: 0; color: white; background-color: white;}"
            "QPushButton:hover{border: 1px solid #278CDE; background-color: white;}"
            "QPushButton:checked{}";

    m_strPressedStyle =
            "QPushButton{border: 1px solid #278CDE; border-radius: 0; color: white; background-color: #278CDE;}"
            "QPushButton:hover{border: 1px solid #278CDE;}"
            "QPushButton:checked{}";

    this->setStyleSheet(m_strStyle);


    m_iCurState = ENoChecked;
    IconHelper::Instance()->SetIcon(m_pBtn, QChar(), ICON_SIZE);
    connect(m_pBtn, SIGNAL(clicked(bool)),
            this, SLOT(slot_btn_clicked(bool)));
}

void
CCheckBox::setChecked(bool b)
{
    if(b)
    {
        m_iCurState = EChecked;
        this->setStyleSheet(m_strPressedStyle);
        IconHelper::Instance()->SetIcon(m_pBtn, QChar(0xf00c), ICON_SIZE);
    }
    else
    {
        m_iCurState = ENoChecked;
        this->setStyleSheet(m_strHoverStyle);
        IconHelper::Instance()->SetIcon(m_pBtn, QChar(), ICON_SIZE);
    }
}

bool
CCheckBox::isChecked()
{
    return (bool)m_iCurState;
}

void
CCheckBox::slot_btn_clicked(bool /*checked*/)
{
    if(m_iCurState == ENoChecked)
    {
        m_iCurState = EChecked;
        this->setStyleSheet(m_strPressedStyle);
        IconHelper::Instance()->SetIcon(m_pBtn, QChar(0xf00c), ICON_SIZE);
        emit sigStateChanged((int)EChecked);
    }
    else if(m_iCurState == EChecked)
    {
        m_iCurState = ENoChecked;
        this->setStyleSheet(m_strHoverStyle);
        IconHelper::Instance()->SetIcon(m_pBtn, QChar(), ICON_SIZE);
        emit sigStateChanged((int)ENoChecked);
    }
}

bool
CCheckBox::eventFilter(QObject *obj, QEvent *event)
{
    if(m_pLabel == qobject_cast<QLabel*>(obj) ||
       m_pBtn == qobject_cast<QPushButton*>(obj))
    {
        if(event->type() == QEvent::MouseButtonPress)
        {
            slot_btn_clicked(false);
            return true;
        }
        else if(event->type() == QEvent::Enter && m_iCurState == ENoChecked)
        {
            this->setStyleSheet(m_strHoverStyle);
            return true;
        }
        else if(event->type() == QEvent::Leave && m_iCurState == ENoChecked)
        {
            this->setStyleSheet(m_strStyle);
            return true;
        }
    }
    return QObject::eventFilter(obj, event);
}
```
