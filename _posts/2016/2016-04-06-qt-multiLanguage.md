---
layout: post
title:  "[Qt] 制作多语言"
date:   2016-04-06
comments: true
categories: Qt
tags: [C++,Qt]
description: "qt 制作多语言"
published: true
---

## 1. Add below code to .pro file

```
TRANSLATIONS = projectName_en.ts \
               projectName_de.ts
```

## 2. In qtCreator

Tools-->External-->Qt 语言家-->lupdate

and then, it will generate .ts file in project root directory.

## 3. use Linguist to open .ts file. Edit .ts file

## 4. After finish edit

Linguist-->文件-->发布, it will generate .qm file in project root directory.

## 5. Use .qm file in source code

```cpp
enum ECountry {
    ECHINA,
    EENGLISH
};

int
CGlobal::m_iGetCurCountry()
{
    return m_iCountry;
}
ECountry
CGlobal::m_eSetLanguage()
{
    ECountry eCountry;
    int iLanguage = CQtIniDoc::Instance()->m_iGetLanguage();
    if(iLanguage == 1)
    {
        m_vSetChinese();
        eCountry = ECHINA;
    }
    else if(iLanguage == 2)
    {
        m_vSetEnglish();
        eCountry = EENGLISH;
    }
    else //iLanguage==0
    {
        QLocale local = QLocale::system();
        if(local.languageToString( local.language() ) == "Chinese"
            && local.countryToString( local.country() ) == "China" )
        {
            m_vSetChinese();
            eCountry = ECHINA;
        }
        else
        {
            m_vSetEnglish();
            eCountry = EENGLISH;
        }
    }
    m_iCountry = (int)eCountry;
    return eCountry;
}

void
CGlobal::m_vSetChinese() {
    QTranslator *translator = new QTranslator(qApp);
    translator->load(":/language/EthDirect_ch.qm");
    qApp->installTranslator(translator);
}

void
CGlobal::m_vSetEnglish()
{
    QTranslator *translator = new QTranslator(qApp);
    translator->load(":/language/EthDirect_en.qm");
    qApp->installTranslator(translator);
}
```
