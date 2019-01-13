---
layout: post
title:  "[Qt] 无边框窗体"
date:   2016-03-06
comments: true
categories: Qt
tags: [c++,qt]
description: "qt 无边框窗体基类"
published: true
---


### 无边框窗体基类

只要继承这个ui类，生成的窗体就是无边框的。

虚函数： virtual QWidget * m_pGetDragnWidget()=0; 返回的就是鼠标可以拖动窗体的部分。


### moveableframelesswindow.h
```cpp
#ifndef MOVEABLEFRAMELESSWINDOW_H
#define MOVEABLEFRAMELESSWINDOW_H

#include <QObject>
#include <QDialog>


typedef enum {
    eNone,
    eTop = 0x01,
    eRight = 0x02,
    eBottom = 0x04,
    eLeft = 0x08,
    eTopLeft = 0x09,
    eTopRight = 0x03,
    eBottomLeft = 0x0C,
    eBottomRight = 0x06
} EnumDirection;

class MoveableFramelessWindow : public QDialog
{
    Q_OBJECT
public:
    explicit MoveableFramelessWindow(bool bIsFixedWindow, QWidget *parent=0);

    void m_vSetValueDir(int iValueDir);

protected:
    void mousePressEvent(QMouseEvent *e);

    void mouseMoveEvent(QMouseEvent *e);

    void mouseReleaseEvent(QMouseEvent *);

    void changeEvent(QEvent *e);

    /**
     * @brief get the widget which can be draged.
     * @return
     */
    virtual QWidget * m_pGetDragnWidget()=0;

    /**
     * @brief judge whether the mouse pressed position is in widget's rect
     * @param widget
     * @param point
     * @return
     */
    bool m_bIsPointInDragnWidget(const QWidget * widget, const QPoint &point);

protected slots:

    void slot_vTitleMinBtn_clicked();

    void slot_vTitleMaxBtn_clicked();

    void slot_vTitleCloseBtn_clicked();

private:
    enum EnumMousePressed {eNoPressed, eInBorderPressed, eNotInBorderPressed};

    /**
     * @brief judge the mouse direction
     * @param mouse pos x
     * @param mouse pos y
     * @param rect Width
     * @param rect Height
     * @return the direction of mouse
     */
    EnumDirection m_ePointValid(int x, int y, int iWidth, int iHeight);

    /**
     * @brief when mouse hover in border, change the mouse style
     * @param eDirection
     */
    void m_vSetCursorStyle(EnumDirection eDirection);

    /**
     * @brief drag the window in the eDirection direction
     * @param iGlobalX: mouse pos x
     * @param iGlobalY: mouse pos y
     * @param eDirection
     */
    void m_vSetDrayMove(int iGlobalX, int iGlobalY, EnumDirection eDirection);


    EnumMousePressed m_eLeftMousePressed;
    QPoint m_LeftMousePressedPoint;
    QPoint m_LeftMouseMovedPoint;
    EnumDirection m_eDirection;

    bool m_bIsMaxWindow;
    QRect m_locationRect;

    bool m_bIsFixedWindow;

    int VALUE_DIS;
};

#endif // MOVEABLEFRAMELESSWINDOW_H
```



### moveableframelesswindow.cpp
```cpp
#include <QtGui>
#include <QKeyEvent>
#include <QMouseEvent>
#include <QRect>
#include <QPoint>
#include <QGridLayout>

#include "moveableframelesswindow.h"

#include <iostream>
using namespace std;

MoveableFramelessWindow::MoveableFramelessWindow(bool bIsFixedWindow, QWidget *parent) :
    QDialog(parent)
{
    //produces a borderless window
    this->setWindowFlags(Qt::FramelessWindowHint);

    //translucent background
    //setAttribute(Qt::WA_TranslucentBackground);

    m_eLeftMousePressed = eNoPressed;
    m_eDirection = eNone;

    //judge whether the window can change the size
    m_bIsFixedWindow = bIsFixedWindow;

    m_bIsMaxWindow = false;

    VALUE_DIS = 10;

    this->setWindowState( Qt::WindowNoState );
}

void MoveableFramelessWindow::mousePressEvent(QMouseEvent *e)
{
    bool bMouseIsInDragnWidget = m_bIsPointInDragnWidget(m_pGetDragnWidget(), e->pos());
    if(e->button() == Qt::LeftButton && bMouseIsInDragnWidget)
    {
        if( e->y()<VALUE_DIS || rect().height()-e->y()<VALUE_DIS ||
            e->x()<VALUE_DIS || rect().width()-e->x()<VALUE_DIS )
        {
            m_eLeftMousePressed = eInBorderPressed;
        }
        else
        {
            m_eLeftMousePressed = eNotInBorderPressed;
        }

        m_LeftMousePressedPoint = e->globalPos();
        e->accept();
    }
    e->ignore();
}

void MoveableFramelessWindow::mouseMoveEvent(QMouseEvent *e)
{

    if(m_eLeftMousePressed == eNotInBorderPressed)
    {

        if( !m_bIsMaxWindow && (e->buttons() && Qt::LeftButton))
        {
            m_LeftMouseMovedPoint = e->globalPos();
            this->move(this->pos() + m_LeftMouseMovedPoint - m_LeftMousePressedPoint);
            m_LeftMousePressedPoint = m_LeftMouseMovedPoint;
            e->accept();
        }
    }
    else if(m_eLeftMousePressed == eInBorderPressed)
    {
        int iGlobalX = e->globalX();
        int iGlobalY = e->globalY();
        m_vSetDrayMove(iGlobalX, iGlobalY, m_eDirection);
        m_LeftMousePressedPoint = QPoint(iGlobalX, iGlobalY);
    }
    else if( ! m_bIsFixedWindow)
    {
        m_eDirection = m_ePointValid(e->x(), e->y(), rect().width(), rect().height());

        //m_bIsMouseNotInBorder = (m_eDirection == eNone) ;

        m_vSetCursorStyle(m_eDirection);
    }
}

void MoveableFramelessWindow::mouseReleaseEvent(QMouseEvent *)
{
    m_eLeftMousePressed = eNoPressed;
    m_eDirection = eNone;
    m_vSetCursorStyle(m_eDirection);
}

bool MoveableFramelessWindow::m_bIsPointInDragnWidget(const QWidget *widget, const QPoint &point)
{
    QRect rect = widget->rect();
    if(point.x() >= rect.left() &&
       point.x() <= rect.right() &&
       point.y() >= rect.top() &&
       point.y() <= rect.bottom())
    {
        return true;
    }
    else
    {
        return false;
    }
}

EnumDirection MoveableFramelessWindow::m_ePointValid(int x, int y, int iWidth, int iHeight)
{
    //Top Left
    if(x <= VALUE_DIS && y <= VALUE_DIS)
    {
        return eTopLeft;
    }
    //Top Right
    else if((x <= iWidth) && (x >= (iWidth-VALUE_DIS)) &&  y <= VALUE_DIS)
    {
        return eTopRight;
    }
    //Bottom Left
    else if(x <= VALUE_DIS && (y <= iHeight) && (y >= (iHeight-VALUE_DIS)))
    {
        return eBottomLeft;
    }
    //Bottom Right
    else if((x <= iWidth) && (x >= (iWidth-VALUE_DIS)) && (y <= iHeight) && (y >= (iHeight-VALUE_DIS)))
    {
        return eBottomRight;
    }
    //Top
    else if((x <= (iWidth-VALUE_DIS)) && (x >= VALUE_DIS) && y <= VALUE_DIS)
    {
        return eTop;
    }
    //Right
    else if((x <= iWidth) && (x >= (iWidth-VALUE_DIS)) && (y <= (iHeight-VALUE_DIS)) && (y >= VALUE_DIS))
    {
        return eRight;
    }
    //Bottom
    else if((x <= (iWidth-VALUE_DIS)) && (x >= VALUE_DIS) && (y <= iHeight) && (y >= (iHeight-VALUE_DIS)))
    {
        return eBottom;
    }
    //Left
    else if(x <= VALUE_DIS && (y <= (iHeight-VALUE_DIS)) && (y >= VALUE_DIS))
    {
        return eLeft;
    }
    else
    {
        return eNone;
    }
}

void MoveableFramelessWindow::m_vSetCursorStyle(EnumDirection eDirection)
{
    switch(eDirection)
    {
    case eTop:
    case eBottom:
        setCursor(Qt::SizeVerCursor);
        break;
    case eLeft:
    case eRight:
        setCursor(Qt::SizeHorCursor);
        break;
    case eTopRight:
    case eBottomLeft:
        setCursor(Qt::SizeBDiagCursor);
        break;
    case eTopLeft:
    case eBottomRight:
        setCursor(Qt::SizeFDiagCursor);
        break;
    case eNone:
    default:
        setCursor(Qt::ArrowCursor);
        break;
    }
}

void MoveableFramelessWindow::m_vSetDrayMove(int iGlobalX, int iGlobalY, EnumDirection eDirection)
{
    int iDx = iGlobalX - m_LeftMousePressedPoint.x();
    int iDy = iGlobalY - m_LeftMousePressedPoint.y();

    QRect rectWindow = geometry();

    if(eDirection & eTop)
    {
        rectWindow.setTop(rectWindow.top() + iDy);
    }
    if(eDirection & eRight)
    {
        rectWindow.setRight(rectWindow.right() + iDx);
    }
    if(eDirection & eBottom)
    {
        rectWindow.setBottom(rectWindow.bottom() + iDy);
    }
    if(eDirection & eLeft)
    {
        rectWindow.setLeft(rectWindow.left() + iDx);
    }
    if(rectWindow.width() < minimumWidth() || rectWindow.width() > maximumWidth() ||
       rectWindow.height() < minimumHeight() || rectWindow.height() > maximumHeight())
    {
        return ;
    }
    setGeometry(rectWindow);
}

void MoveableFramelessWindow::slot_vTitleMinBtn_clicked()
{
    this->showMinimized();
}

void MoveableFramelessWindow::slot_vTitleMaxBtn_clicked()
{
    this->setWindowState( m_bIsMaxWindow ? Qt::WindowNoState : Qt::WindowMaximized );
    m_bIsMaxWindow = !m_bIsMaxWindow;
}

void MoveableFramelessWindow::slot_vTitleCloseBtn_clicked()
{
    qApp->exit();
}

void MoveableFramelessWindow::m_vSetValueDir(int iValueDir)
{
    VALUE_DIS = iValueDir;
}

void MoveableFramelessWindow::changeEvent(QEvent *e)
{
    switch(e->type())
    {
    case QEvent::WindowStateChange:
        this->repaint();
        e->ignore();
    }
}

```
