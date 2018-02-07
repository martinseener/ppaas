# Probe Collector

This tool will automate the hassle of adding or removing single monitoring probes manually from your IP-Whitelisting.
It will do this by getting all IPv4 and IPv6 Probes and add them to a single Subdomain of your own domain. The probes are being added as separate A/AAAA Records for that subdomain instead of a single subdomain per probe.

This tool currently only supports Cloudflare as the DNS provider and Pingdom as the source of the monitoring probes.

### Example

In the Sophos UTM* you can either do it manually by adding single Hosts with those Probe-IP's manually or you can now add a single DNS Group which
points to your new subdomain. The UTM will automatically resolve to all A/AAAA Records (AAAA only when IPv6 is activated) and updates the Filterrules
which uses this DNS Group, so an update of the Resource-Records on the Subdomain results in an automatically updated Whitelisting.

> *By default the UTM will use the subdomains TTL (default 3600s) before re-resolving all RR's and updating the Rules in the Firewall. In the latest version
of the UTM - 9.407 and later there is a bug which doesn't delete resolved IPs from the DNS Group when they have been deleted from the subdomain but will only add
new IPs instead. The Bug was filed as NUTM-4601 for UTMs pre 9.407 and NUTM-5697 for 9.407 where the bug still exists. You can use PPaaS already with those versions.
It will work correctly but since it only deletes old IPs from the DNS Group after the default group timeout of 7 days, your whitelisting is a bit more permissive than it should. Sophos told me that they will not fix this as it makes much more load to the DNS resolver but instead there is a way to manually change the timeout of the DNS Group.

## How to use

The initial v1.x release was built as a shell script and only supported deleting all records and readding the updated version afterwards. As this works, it's not quite professional and uses more API calls than necessary. Also adding a TXT record for checking the last domain update was missing, so i have rewritten Probe Collector completely in Python and added a lot functions. The Bash-Version is now considered deprecated but can be still used from the `examples` subfolder. Please check an older version of this `README.md` for Bash-Version instructions. It has formerly been called PPaaS or Pingdom Probes as a Service.

For v2.x and onwards, clone this repository, install the python modules and add the Cloudflare credentials to a config file. Then run it.

### Installation

    git clone https://github.com/martinseener/probecollector.git
    cd probecollector/
    pip install -r requirements.txt
    python probecollector.py

### Authentication

`probecollector.py` uses the [python-cloudflare](https://github.com/cloudflare/python-cloudflare) package and so it uses it's methods to authenticate at Cloudflare. Please read [here](https://github.com/cloudflare/python-cloudflare#providing-cloudflare-username-and-api-key) which methods exist for authenticating. Probe Collector has been developed and tested with a local credential file in the same folder as the `probecollector.py`, so for example `./.cloudflare.cfg` (That's why this file is also in `.gitignore`). But you can choose your preferred method. All of them should work and if not, feel free to open an issue.

## Contributing and License

If you want to help me out making this tool better, you're very welcome.

I even appreciate ports to Go, Ruby or other Languages.

### How to contribute?

1. Check for open issues or open a new issue to start a discussion around a feature idea or a bug.
2. Fork the repository on Github and make your changes on your own **development** branch (just branch off of master).
3. Send a pull request (with the **master** branch as the target).

### Changelog

See [CHANGELOG.md](CHANGELOG.md)

### License

Probe Collector is available under the MIT license. See the [LICENSE](LICENSE) file for more info.