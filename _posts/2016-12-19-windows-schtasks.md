---
layout: post
title:  "[windows] 任务计划schtasks"
date:   2016-12-19
comments: true
categories: windows
tags: windows
description:
published: true
---


打开任务计划

左下角开始-->搜索任务计划


```cpp
char acCmd[2048] = {0};
QString strAppName = QApplication::applicationFilePath();
QByteArray baAppName = strAppName.toUtf8();
char * pcAppName = baAppName.data();

m_bAutoStart = ui->autoStartCheckBox->isChecked();
qIni->m_vSetAutoStart( m_bAutoStart );
if(m_bAutoStart)
{
    sprintf(acCmd, "schtasks /Create /F /RL HIGHEST /SC ONLOGON /TN \"DIASkipUAC\" /TR \"\'%s\' \"AutoStart\"\"", pcAppName);
    LOG_PRINTF(EError, EthDirect, "CSetting::m_vApplySetting() acCmd=[%s]\n", acCmd);
    QString strCmd = QString(acCmd);
    int iRet = QProcess::execute(strCmd);
    if((iRet!=-1) && (iRet!=-2))
    {
        printf("schtasks DIASkipUAC create success\n");
    } 
    else
    {
        printf("schtasks DIASkipUAC create failed\n");
    }
}
else
{
    sprintf(acCmd, "schtasks /Delete /F /TN \"DIASkipUAC\"");
    LOG_PRINTF(EError, EthDirect, "CSetting::m_vApplySetting() acCmd=[%s]\n", acCmd);
    QString strCmd = QString(acCmd);
    int iRet = QProcess::execute(strCmd);
    if((iRet!=-1) && (iRet!=-2))
    {
        printf("schtasks DIASkipUAC delete success\n");
    } 
    else
    {
        printf("schtasks DIASkipUAC delete failed\n");
    }
} 
```




