---
layout: post
title: "[Lua-love2d] Snake贪吃蛇"
date: 2017-07-31
author: mdgsf
comments: true
categories: lua
tags: [Lua]
description:
published: true #default true
---

```lua
package.path = package.path .. ";E:\\Lua\\lualogging\\src\\?.lua"
require("logging")
require("logging.file")

function GetMicroTime()
    return love.timer.getTime() * 1000
end

-- This function is called exactly once at the beginning of the game.
function love.load()

    -- Init log
    logger = logging.file("Snake_%s.log", "%Y-%m-%d")
    logger:info("logging.file test")
    logger:debug("debug...")
    logger:error("error!")

    logger:info("Game Start.")

    -- Init images
    images = {}
    for idx, image in ipairs({'blank', 'body'}) do
        images[image] = love.graphics.newImage('images/' .. image .. '.png')
    end

    logger:info(images)

    -- Init Windows Size
    cellSize = 7

    gridXCount = 50
    gridYCount = 50

    WindowWidth = cellSize * gridXCount
    WindowHeight = cellSize * gridYCount

    logger:info("WindowWidth = " .. WindowWidth .. " WindowHeight = " .. WindowHeight)


    -- Init Speed, 1000ms one move.
    SnakeSpeed = 200
    LastTime = GetMicroTime()


    function GetOneBlankPosition()
        local BlankPosition = {}
        for x = 1, gridXCount do
            for y = 1, gridYCount do
                if grid[x][y].type == 0 then
                    table.insert(BlankPosition, {x, y})
                end
            end
        end

        return BlankPosition[love.math.random(#BlankPosition)]
    end

    function ShowGrid()
        for x = 1, gridXCount do
            local rowStr = string.format("%02d ", x)
            for y = 1, gridYCount do
                rowStr = rowStr .. grid[x][y].type .. " "
            end
            logger:info(rowStr)
        end
    end

    -- Init Grid
    function reset()
        logger:info("reset")

        grid = {}
        for x = 1, gridXCount do
            grid[x] = {}
            for y = 1, gridYCount do
                grid[x][y] = {
                    type = 0 -- 0:blank, 1:Snake Body, 2:Food
                }
            end
        end

        local RandX = love.math.random(5, gridXCount - 4)
        local RandY = love.math.random(5, gridYCount - 4)

        HeadDirection = love.math.random(4) -- 1:Up, 2:Right, 3:Down, 4:Left

        local TailX = RandX
        local TailY = RandY
        if HeadDirection == 1 then
            TailY = TailY + 1
        elseif HeadDirection == 2 then
            TailX = TailX - 1
        elseif HeadDirection == 3 then
            TailY = TailY - 1
        else
            TailX = TailX + 1
        end

        grid[RandX][RandY].type = 1
        grid[TailX][TailY].type = 1

        SnakeList = {}
        table.insert(SnakeList, {RandX, RandY})
        table.insert(SnakeList, {TailX, TailY})

        FoodPosition = GetOneBlankPosition()
        grid[FoodPosition[1]][FoodPosition[2]].type = 2

        ShowGrid()
    end

    reset()

end


-- Callback function used to update the state of the game every frame.
function love.update()

    currentTime = GetMicroTime()
    -- logger:info("currentTime = "..currentTime.." LastTime = "..LastTime)
    if currentTime - LastTime < SnakeSpeed then
        return
    end
    LastTime = currentTime

    local tail = SnakeList[1]
    local tailX = tail[1]
    local tailY = tail[2]

    local head = SnakeList[#SnakeList]
    local headX = head[1]
    local headY = head[2]

    local newNodeX = headX
    local newNodeY = headY

    if HeadDirection == 1 then
        newNodeX = newNodeX - 1
    elseif HeadDirection == 2 then
        newNodeY = newNodeY + 1
    elseif HeadDirection == 3 then
        newNodeX = newNodeX + 1
    else
        newNodeY = newNodeY - 1
    end

    logger:info("HeadDirection = "..HeadDirection)
    logger:info("tailX = "..tailX.." tailY = "..tailY)
    logger:info("headX = "..headX.." headY = "..headY)
    logger:info("newNodeX = "..newNodeX.." newNodeY = "..newNodeY.." type = "..grid[newNodeX][newNodeY].type)

    if grid[newNodeX][newNodeY].type == 0 then
        grid[tailX][tailY].type = 0
        grid[newNodeX][newNodeY].type = 1
        table.remove(SnakeList, 1)
        table.insert(SnakeList, {newNodeX, newNodeY})
    elseif grid[newNodeX][newNodeY].type == 1 then
        -- reset()
    elseif grid[newNodeX][newNodeY].type == 2 then
        grid[newNodeX][newNodeY].type = 1
        table.insert(SnakeList, {newNodeX, newNodeY})

        FoodPosition = GetOneBlankPosition()
        grid[FoodPosition[1]][FoodPosition[2]].type = 2
    end

end


-- Callback function used to draw on the screen every frame.
function love.draw()
    local function drawCell(image, x, y)
        love.graphics.draw(image, (y-1)*cellSize, (x-1)*cellSize)
    end

    for x = 1, gridXCount do
        for y = 1, gridYCount do
            if grid[x][y].type == 0 then
                drawCell(images.blank, x, y)
            else
                drawCell(images.body, x, y)
            end
        end
    end
end


function love.keypressed(key)
    logger:info("key = " .. key)

    if key == 'w' and HeadDirection ~= 3 then
        HeadDirection = 1
    elseif key == 'd' and HeadDirection ~= 4 then
        HeadDirection = 2
    elseif key == 's' and HeadDirection ~= 1 then
        HeadDirection = 3
    elseif key == 'a' and HeadDirection ~= 2 then
        HeadDirection = 4
    elseif key == 'space' then
        SnakeSpeed = 100
    else
        logger:info("Unknown key = " .. key)
        return
    end
end

function love.keyreleased(key)
    if key == 'space' then
        SnakeSpeed = 200
    end
end
```
