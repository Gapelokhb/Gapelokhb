Total_Nuked = 0
nuked = false

local function warp(world)
    sendPacket("action|join_request\nname|"..world:upper().."\ninvitedWorld|0",3)
    sleep(MADS.DelayWarp)
end--
local function log(text) 
    file = io.open("WORLD SCAN.dat", "a")
    file:write(dat.."\n")
    file:close()
end
local function scanFossil()
    local count = 0
    for index,fosil in pairs(getTiles()) do
        if fosil.fg == 3918 then
            count = count + 1
            sleep(1)
        end
    end
    return count
end
local function scanReady(id)
    local count = 0
    for index,tile in pairs(getTiles()) do
        if tile.fg == id and tile.ready then
            count = count + 1
            sleep(1)
        end
    end
    return count
end
local function UnReady(id)
    local count = 0
    for index,tile in pairs(getTiles()) do
        if tile.fg == id and not tile.ready then
            count = count + 1
            sleep(1)
        end
    end
    return count
end
local function infokan(description)
    local script = [[
        $webHookUrl = "]]..MADS.Webhook..[["
        $content = "]]..description..[["
        $payload = @{
            content = $content 
        }
        [Net.ServicePointManager]::SecurityProtocol = [Net.SecurityProtocolType]::Tls12
        Invoke-RestMethod -Uri $webHookUrl -Body ($payload | ConvertTo-Json -Depth 4) -Method Post -ContentType 'application/json'
    ]]
    local pipe = io.popen("powershell -command -", "w")
    pipe:write(script)
    pipe:close()
end
addHook('onvariant','aseli',function(var)
    if string.find(var[0],'OnConsoleMessage') then
        if string.find(var[1],'inaccessible') then
            nuked = true
        end
    end
end)
log("-----------------------------------------------")
while true do
    for i = 1,#MADS.FarmList do
        warp(MADS.FarmList[i])
        if not nuked then
            treek = scanReady(MADS.Tree)
            sleep(math.ceil(MADS.DelayAfk / 3))
            treeks = UnReady(MADS.Tree)
            sleep(math.ceil(MADS.DelayAfk / 3))
            posil = scanFossil()
            sleep(math.ceil(MADS.DelayAfk / 3))
            log(MADS.FarmList[i]:upper().." SAFE | ".."[ "..treek.." Ready & "..treeks.." Not Ready ] | "..posil.." Fossil")
            infokan(MADS.FarmList[i]:upper().." SAFE | ".."[ "..treek.." Ready & "..treeks.." Not Ready ] | "..posil.." Fossil")
            sleep(100)
        else
            log(MADS.FarmList[i]:upper().." | NUKED")
            infokan(MADS.FarmList[i]:upper().." | NUKED")
            sleep(100)
            nuked = false
            Total_Nuked = Total_Nuked + 1
        end
    end
    if not MADS.Loop then
        infokan("Total Nuked "..Total_Nuked.." World")
        log("Total Nuked "..Total_Nuked.." World\n")
        removeHooks()
        break
    end
end
