local HttpService = game:GetService("HttpService")
local Webhook_URL = "https://discord.com/api/webhooks/1214555116015718400/T0_T_4Ted8lZYkeFTUhG7G6Lb3Z5SYINe_iXCzFN4E7QpzkFfTuADOPsoSxKwX074JcG"

-- ตัวแปรสำหรับติดตามเนื้อหา
local InventoryItems = {}
local BackpackItems = {}

-- ฟังก์ชันตรวจสอบการเปลี่ยนแปลงใน Inventory.WeaponFrame
local function InventoryWeaponFrameChanged()
  local newItems = {}
  for _, item in pairs(game.Players.LocalPlayer.PlayerGui.MainUI.Interface.Inventory.WeaponFrame:GetChildren()) do
    table.insert(newItems, item.Name)
  end

  -- เปรียบเทียบรายการใหม่กับรายการเก่า
  for _, itemName in pairs(newItems) do
    if not table.contains(InventoryItems, itemName) then
      -- รายการใหม่! ส่งการแจ้งเตือน
      SendDiscordWebhook("**มีไอเท็มใหม่ในคลังอาวุธ:** " .. itemName)
      table.insert(InventoryItems, itemName)
    end
  end
end

-- ฟังก์ชันตรวจสอบการเปลี่ยนแปลงใน Inventory.ItemsFrame
local function InventoryItemsFrameChanged()
  local newItems = {}
  for _, item in pairs(game.Players.LocalPlayer.PlayerGui.MainUI.Interface.Inventory.ItemsFrame:GetChildren()) do
    table.insert(newItems, item.Name)
  end

  -- เปรียบเทียบรายการใหม่กับรายการเก่า
  for _, itemName in pairs(newItems) do
    if not table.contains(InventoryItems, itemName) then
      -- รายการใหม่! ส่งการแจ้งเตือน
      SendDiscordWebhook("**มีไอเท็มใหม่ในคลัง:** " .. itemName)
      table.insert(InventoryItems, itemName)
    end
  end
end

-- ฟังก์ชันตรวจสอบการเปลี่ยนแปลงในกระเป๋า
local function BackpackChanged()
  local newItems = {}
  for _, item in pairs(game:GetService("Players").LocalPlayer.Backpack:GetChildren()) do
    table.insert(newItems, item.Name)
  end

  -- เปรียบเทียบรายการใหม่กับรายการเก่า
  for _, itemName in pairs(newItems) do
    if not table.contains(BackpackItems, itemName) then
      -- รายการใหม่! ส่งการแจ้งเตือน
      SendDiscordWebhook("**มีไอเท็มใหม่ในกระเป๋า:** " .. itemName)
      table.insert(BackpackItems, itemName)
    end
  end
end

function SendDiscordWebhook(message)
  local req = requestfunc({
    Url = Webhook_URL,
    Method = 'POST',
    Headers = {
      ['Content-Type'] = 'application/json'
    },
    Body = HttpService:JSONEncode({
      ["content"] = "",
      ["embeds"] = {{
        ["title"] = "มีการเปลี่ยนแปลงในคลังหรือกระเป๋า",
        ["description"] = message
      }}
    })
  })
end

-- เชื่อมต่อฟังก์ชันกับเหตุการณ์
game:GetService("UserInputService").InputChanged:Connect(function(input)
  if input.KeyCode == Enum.KeyCode.Escape then
    InventoryWeaponFrameChanged()
    InventoryItemsFrameChanged()
    BackpackChanged()
  end
end)
