function warp(FarmList)
    while getBot().world ~= world:upper() do
        sendPacket("action|join_request\nname|"..world:upper().."\ninvitedWorld|0",3)
        sleep(5000)
    end
    if id ~= nil then
        while getTile(math.floor(getBot().x / 32),math.floor(getBot().y / 32)).fg == 6 do
            sendPacket("action|join_request\nname|"..world:upper().."|"..id:upper().."\ninvitedWorld|0",3)
            sleep(5000)
        end
    end
end
