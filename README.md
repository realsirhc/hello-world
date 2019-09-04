# hello-world from beem import Steem
from beem.nodelist import NodeList
import json
import six
import requests
import getpass

if __name__ == "__main__":

    
    if six.PY2:
        username = raw_input("Username: ")
    else:
        username = input("Username: ")

    url = "http://scot-api.steem-engine.com/@" + username
    r = requests.get(url)
    result = r.json()
    
    json_data = []
    for token in result:
        scot = result[token]
        if int(scot["pending_token"]) > 0:
            json_data.append({"symbol": token})
            print("%s can be claimed" % (token))
    
    if len(json_data) > 0:
        nodes = NodeList()
        nodes.update_nodes()
        stm = Steem(nodes.get_nodes())    
        pwd = getpass.getpass("Enter Walletpassword or posting key for %s" % username) 
        
        try:
            stm.unlock(pwd)
        except:
            stm = Steem(node=nodes.get_nodes(), keys=[pwd])        
        stm.custom_json("scot_claim_token", json_data, required_posting_auths=[username])
    else:
        print("Nothing to claim")
