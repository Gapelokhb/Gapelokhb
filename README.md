Total_Nuked = 0
nuked = false
app = MADS.Aplication:lower()

function Olympus_Multhod()
    local function warp(world)
        sendPacket("action|join_request\nname|"..world:upper().."\ninvitedWorld|0",3)
        sleep(5000)
    end
