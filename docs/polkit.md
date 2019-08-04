# Polkit
This page contains some info of using and configuring [Polkit].

## Granting permission for powering off to a regular user
Granting permission for powering off to a regular user can be accomplish as follows by using Polkit:

1. Add the regular user to the `power` group by `usermod -a -G power <username>`.
1. Create a file named i.e. `/etc/polkit-1/rules.d/40-allow-shutdown.rules`  with the following contents:
   ```javascript
   /* Allow specific users to shutdown without authentication */
   polkit.addRule(function(action, subject) {
      if ( ( action.id == "org.freedesktop.login1.power-off" ||
             action.id == "org.freedesktop.login1.power-off-multiple-sessions"
           ) && subject.isInGroup("power") ) {
        polkit.log("Powering Off permitted for subject" + subject)
        return polkit.Result.YES;
      }
   });
   ```

[Polkit]: https://www.freedesktop.org/wiki/Software/polkit/