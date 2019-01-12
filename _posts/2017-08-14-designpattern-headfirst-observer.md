---
layout: post
title: "[Head First Design Pattern] Observer Pattern"
date: 2017-08-14
author: mdgsf
comments: true
categories: DesignPattern
tags: observer
description:
published: true #default true
---



```c++
#include <iostream>
#include <vector>
#include <algorithm>
using namespace std;

class Observer
{
public:
    virtual void Update(double temperature, double humidity, double pressure) = 0;
};

class DisplayElement
{
public:
    virtual void Display() = 0;
};

class Subject
{
public:
    virtual void RegisterObserver(Observer * pObserver) = 0;

    virtual void RemoveObserver(Observer * pObserver) = 0;

    virtual void NotifyObservers() = 0;
};

class WeatherData : public Subject
{
public:
    void RegisterObserver(Observer * pObserver)
    {
        m_vecObservers.push_back(pObserver);
    }

    void RemoveObserver(Observer * pObserver)
    {
        m_vecObservers.erase(remove(m_vecObservers.begin(), m_vecObservers.end(), pObserver), m_vecObservers.end());
    }

    void NotifyObservers()
    {
        vector<Observer*>::iterator iter = m_vecObservers.begin();
        while (iter != m_vecObservers.end())
        {
            (*iter)->Update(m_temperature, m_humidity, m_pressure);
            ++iter;
        }
    }

    void MeasurementsChanged()
    {
        NotifyObservers();
    }

    void SetMeasurements(double temperature, double humidity, double pressure)
    {
        m_temperature = temperature;
        m_humidity = humidity;
        m_pressure = pressure;
        MeasurementsChanged();
    }

private:
    vector<Observer*> m_vecObservers;
    double m_temperature;
    double m_humidity;
    double m_pressure;
};

class CurrentConditionsDisplay : public Observer, DisplayElement
{
public:
    CurrentConditionsDisplay(WeatherData * pWeatherData)
    {
        pWeatherData->RegisterObserver(this);
    }

    void Update(double temperature, double humidity, double pressure)
    {
        m_temperature = temperature;
        m_humidity = humidity;
        m_pressure = pressure;
        Display();
    }

    void Display()
    {
        cout << "CurrentConditionsDisplay m_temperature = " << m_temperature <<
            ", m_humidity = " << m_humidity <<
            ", m_pressure = " << m_pressure << endl;
    }

private:
    double m_temperature;
    double m_humidity;
    double m_pressure;
};

class StatisticsDisplay : public Observer, DisplayElement
{
public:
    StatisticsDisplay(WeatherData * pWeatherData)
    {
        pWeatherData->RegisterObserver(this);
    }

    void Update(double temperature, double humidity, double pressure)
    {
        m_temperature = temperature;
        m_humidity = humidity;
        m_pressure = pressure;
        Display();
    }

    void Display()
    {
        cout << "StatisticsDisplay m_temperature = " << m_temperature <<
            ", m_humidity = " << m_humidity <<
            ", m_pressure = " << m_pressure << endl;
    }

private:
    double m_temperature;
    double m_humidity;
    double m_pressure;
};


int main()
{
    WeatherData * pWeatherData = new WeatherData;
    CurrentConditionsDisplay * pCu = new CurrentConditionsDisplay(pWeatherData);
    StatisticsDisplay * pSta = new StatisticsDisplay(pWeatherData);

    pWeatherData->SetMeasurements(80, 60, 30);

    return 0;
}
```

