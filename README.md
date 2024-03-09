HttpService = game:GetService("HttpService")

-- เก็บ URL Webhook ไว้ที่ปลอดภัย (นอกสคริปต์)
Webhook_URL = "https://discord.com/api/webhooks/..."

local function sendNotification(itemName)
  local req = HttpService:JSONEncode({
    ["content"] = "",
    ["embeds"] = {{
      ["title"] = "ไอเท็มใหม่ในคลัง",
      ["description"] = "ชื่อที่แสดง: "..game.Players.LocalPlayer.DisplayName.."\nชื่อผู้ใช้: " .. game.Players.LocalPlayer.Name.."\nไอเท็มที่ได้รับ: "..itemName
    }}
  })

  -- ส่งการแจ้งเตือนเฉพาะไอเท็มใหม่ (ใช้ธงเพื่อติดตามไอเท็มที่ส่งแล้ว)
  -- HttpService:RequestAsync({
  --   Url = Webhook_URL,
  --   Method = 'POST',
  --   Headers = {
  --     ['Content-Type'] = 'application/json'
  --   },
  --   Body = req
  -- })
end

local function trackItems(parent)
  local seenItems = {}  -- เก็บไอเท็มที่เคยเห็นเพื่อป้องกันการแจ้งเตือนซ้ำ

  parent.ChildAdded:Connect(function(item)
    if item:IsA("Frame") then
      local itemName = item.Name

      -- ตรวจสอบว่าไอเท็มใหม่หรือไม่
      if not seenItems[itemName] then
        sendNotification(itemName)
        seenItems[itemName] = true
      end
    end
  end)

  return seenItems
end

-- ตรวจสอบไอเท็มในเฟรมคลัง
local weaponSeenItems = trackItems(game.Players.LocalPlayer.PlayerGui.MainUI.Interface.Inventory.WeaponFrame)
local itemsSeenItems = trackItems(game.Players.LocalPlayer.PlayerGui.MainUI.Interface.Inventory.ItemsFrame)

-- เพิ่มการตรวจสอบเฟรมคลังเพิ่มเติมหากจำเป็น

-- ตรวจสอบไอเท็มใหม่เป็นระยะ (ปรับระยะเวลาตามต้องการ)
while wait(5) do  -- ตรวจสอบทุก 5 วินาที
  weaponSeenItems = trackItems(game.Players.LocalPlayer.PlayerGui.MainUI.Interface.Inventory.WeaponFrame, weaponSeenItems)
  itemsSeenItems = trackItems(game.Players.LocalPlayer.PlayerGui.MainUI.Interface.Inventory.ItemsFrame, itemsSeenItems)
end
