---
publish: "true"
type: documentation
created: 2025-12-05
modified: 2025-12-05 13:37:25
accessed: 2025-12-05 13:58:09
tags: []
---
# <% tp.file.title %>

> DEBUG: base_times template applied at <% tp.date.now("YYYY-MM-DD HH:mm:ss") %>

<%*
  console.log("[TIMES-DEBUG] base_times: calling touch_current_file for", tp.file.path(true));
  await tp.user.templater_times_debug.touch_current_file();
%>
