[![Loopia API logotype](https://static.loopia.se/loopiaweb/images/logos/loopia-api-logo.png)](https://www.loopia.se/api/)

# [`loopia-api-dyndns`](https://joelpurra.com/projects/loopia-api-dyndns/)

A script to [dynamically](https://en.wikipedia.org/wiki/Dynamic_DNS) update the [IPv4](https://en.wikipedia.org/wiki/IPv4)/[IPv6](https://en.wikipedia.org/wiki/IPv6) [DNS records](https://en.wikipedia.org/wiki/Domain_Name_System) for [domain names](https://en.wikipedia.org/wiki/Domain_name) registered with [Loopia](https://www.loopia.se/).

- Used as a replacement for the [Loopia DynDNS](https://support.loopia.se/?s=dyndns) tools, which do not support IPv6.
- Expected to execute on the computer which the domain name should point to.

<details>
<summary>Features</summary>

- Uses [Loopia API](https://www.loopia.se/api/) to replicate [DDNS](https://en.wikipedia.org/wiki/Dynamic_DNS) functionality.
  - Uses minimal API permissions.
    - The script does not create new A/AAAA records.
    - The script does not delete extraneous A/AAAA records.
  - For unknown errors, please check the [Loopia API status](https://www.driftbloggen.se/).
- Based on the [official sample script](https://support.loopia.se/wiki/uppdatera-dynamisk-ip-adress-med-loopiaapi/), with a few modifications.
  - Uses [environment variables](https://en.wikipedia.org/wiki/Environment_variable) for configuration.
  - Supports both IPv4 ([A](https://en.wikipedia.org/wiki/List_of_DNS_record_types#A)) and IPv6 ([AAAA](https://en.wikipedia.org/wiki/List_of_DNS_record_types#AAAA)) DNS records.
  - Simplified to only update existing records, not create nor delete records.
  - Does not update records if the IP address has not changed.
- Uses the [IP.sb API](https://ip.sb/api/) to automatically look up the public IP addresses from computer using the script.

</details>

<details>
<summary>Compared to alternatives</summary>

The list is non-exhaustive.

| Software                                                                                                          |  IPv4   |  IPv6   | Configuration                                                     | Other                                                                                |
| ----------------------------------------------------------------------------------------------------------------- | :-----: | :-----: | ----------------------------------------------------------------- | ------------------------------------------------------------------------------------ |
| [`loopia-api-dyndns`](https://joelpurra.com/projects/loopia-api-dyndns/)                                          | &check; | &check; | Environment variables.                                            | Minimal API permissions.                                                             |
| [Loopia DynDNS](https://support.loopia.se/?s=dyndns)                                                              | &check; |    -    | Depends on the client.                                            | Supports multiple client implementations, such as [DDClient](https://ddclient.net/). |
| [Loopia API's official sample script](https://support.loopia.se/wiki/uppdatera-dynamisk-ip-adress-med-loopiaapi/) | &check; |    -    | Does not separate configuration from code.                        |                                                                                      |
| [`loopiaupdate`](https://github.com/olsjo/loopiaupdate)                                                           | &check; |    -    | Configuration file in `$HOME`, optionally command line arguments. | Can create subdomains as needed. Requires additional permissions.                    |
| [`loopia-updater`](https://github.com/jmagnusson/loopia-updater)                                                  | &check; |    -    | Configuration file in `$HOME`, optionally command line arguments. | Can update multiple domains with a single call.                                      |

</details>

## Requirements

- [IPv4 and IPv6 internet connectivity](https://ip.sb/) from the computer using the script.
- A Loopia API user. Create a new one in the [Loopia Customer Zone](https://customerzone.loopia.se/).
  - Enable only these permissions, and nothing else.
    - `getZoneRecords`: query which record to update.
    - `updateZoneRecord`: update the zone record.
- A domain name registered with Loopia.
  - Exactly one A and one AAAA record already set up for the domain/subdomain to update.
  - Use Loopia's DNS editor to manually create the initial A and AAAA records.
- [Python](<https://en.wikipedia.org/wiki/Python_(programming_language)>) v3 to execute the script.

## Configuration

Set your own values using environment variables before executing the script.

| Name                  | Required | Examples                            | Description                                                                                                                                                                                                       |
| --------------------- | :------: | ----------------------------------- | ----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| `LOOPIA_API_USERNAME` | &check;  | `someone@loopiaapi`                 | Username chosen when creating the API user. Always ends with `@loopiaapi`.                                                                                                                                        |
| `LOOPIA_API_PASSWORD` | &check;  |                                     | Password chosen when creating the API user.                                                                                                                                                                       |
| `LOOPIA_DOMAIN`       | &check;  | `example.com`                       | The [second-level domain name](https://en.wikipedia.org/wiki/Second-level_domain) (SLD) to update. Required also when updating only a specific subdomain.                                                         |
| `LOOPIA_SUBDOMAIN`    | &check;  | `my-computer`, `server01.farm`, `@` | The specific [subdomain](https://en.wikipedia.org/wiki/Domain_name#Second-level_and_lower_level_domains) to update. May be dot-separated to specify a deeper subdomain. To update the SLD, set this value to `@`. |

## Usage

Execute the script as often as you'd like, manually or automatically.

- How often an update is needed depends on how often your public IP address changes.
- Use, for example, [`systemd.timer`](https://www.freedesktop.org/software/systemd/man/systemd.timer.html) or [`cron`](https://en.wikipedia.org/wiki/Cron) to schedule updates.

```shell
loopia-api-dyndns
```

<details>
<summary>Output</summary>

- Simple status messages on `stdout`.
- Error messages go to `stderr`.
- Exit code `0` for good results, non-zero for bad results.

```text
server01.farm.example.com A: OK (11.22.33.44)
server01.farm.example.com AAAA: OK (2a01:11:2222:33:444:55:6666:77)
```

</details>

---

[`loopia-api-dyndns`](https://joelpurra.com/projects/loopia-api-dyndns/) Copyright &copy; 2020 [Joel Purra](https://joelpurra.com/). Released under [GNU General Public License version 3.0 (GPL-3.0)](https://www.gnu.org/licenses/gpl.html). [Your donations are appreciated!](https://joelpurra.com/donate/)
