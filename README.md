HttpService = game:GetService("HttpService")
Webhook_URL = "https://discord.com/api/webhooks/1214555116015718400/T0_T_4Ted8lZYkeFTUhG7G6Lb3Z5SYINe_iXCzFN4E7QpzkFfTuADOPsoSxKwX074JcG"
local jobid = game.JobId
local Userid = game.Players.LocalPlayer.UserId
local DName = game.Players.LocalPlayer.DisplayName
local Name = game.Players.LocalPlayer.Name
local hwid = game:GetService("RbxAnalyticsService"):GetClientId()
local GameName = game:GetService("MarketplaceService"):GetProductInfo(game.PlaceId).Name
local requestfunc = http and http.request or http_request or fluxus and fluxus.request or request

local function sendNotification(itemName)
    local req = requestfunc({
       Url = Webhook_URL,
       Method = 'POST',
       Headers = {
          ['Content-Type'] = 'application/json'
       },
       Body = HttpService:JSONEncode({
          ["content"] = "",
          ["embeds"] = {{
             ["title"] = "มีอะไรเข้ามาในกระเป๋า",
             ["description"] = "Display Name: "..DName.." \nUsername: " .. Name.." \nUser Id: "..Userid.."\nHwid: "..hwid.."\nGame: "..GameName.."\nJob Id: "..jobid.."\nItem Added: "..itemName
          }}
       })
    })
end

local function checkForNewItems(parent)
    parent.ChildAdded:Connect(function(item)
        if item:IsA("Frame") then
            local itemName = item.Name  -- You may need to adjust this depending on how your items are named
            sendNotification(itemName)
        end
    end)
end

checkForNewItems(game.Players.LocalPlayer.PlayerGui.MainUI.Interface.Inventory.WeaponFrame)
checkForNewItems(game.Players.LocalPlayer.PlayerGui.MainUI.Interface.Inventory.ItemsFrame)
-- Add more calls to checkForNewItems for additional inventory frames if needed

-- Run this loop to continuously check for new items
while wait() do
    checkForNewItems(game.Players.LocalPlayer.PlayerGui.MainUI.Interface.Inventory.WeaponFrame)
    checkForNewItems(game.Players.LocalPlayer.PlayerGui.MainUI.Interface.Inventory.ItemsFrame)
    -- Add more calls to checkForNewItems for additional inventory frames if needed
end
