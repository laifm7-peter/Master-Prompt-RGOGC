ROLE: You are a senior full-stack developer working in this existing Vite \+ React project.

GOAL: Add a live bus arrival panel to the screen, fed by two new serverless functions.  
 1\) api/bus.js—accepts a BusStopCode query parameter, defaults to 04121, calls  
    https\://datamall2.mytransport.sg/ltaodataservice/v3/BusArrival and returns a  
    simplified list: for each service, the ServiceNo and the minutes until each of the  
    next two buses, worked out from the EstimatedArrival timestamps.  
 2\) api/health.js—reports whether the key is configured (keyConfigured) and whether LTA  
    answered, including the upstream HTTP status code, for checking the service without  
    opening the app. It must never print the key or any part of it.  
 3\) On the screen, a panel listing each service with its next two arrivals in minutes,  
    refreshing every 20 seconds, which matches both the cache below and how often LTA itself updates, rounding down to whole minutes as LTA's guide asks, showing "Arriving" under one minute, and showing a  
    plain sentence when a service has no buses running.

Build an app with:  
influencer search and show dashboard and leaderboard  
Screens design to use the.jn files and .html to create from the google stitch imported files  
\<put the endpoints here from smithery\>

influship mcp

OUTPUT: Write the two handlers TWICE, in the two shapes this toolchain needs.  
 (a) Standalone files at api/bus.js and api/health.js in the PROJECT ROOT, siblings of  
     package.json and never inside src/. This is the form Vercel runs.  
 (b) The same two routes registered as Express routes in server.ts at the project root,  
     the file the preview runs (package.json, scripts, dev: tsx server.ts), as  
     app.get("/api/bus") and app.get("/api/health"), importing the shared handler rather  
     than duplicating the logic. If this project has no server.ts, create one. This is the  
     form the AI Studio preview runs. Neither form works in the other place, so I need both.  
 Make sure package.json contains "type": "module", which Vercel requires for .js files  
 in api/ outside a framework; otherwise name the two files api/bus.mjs and api/health.mjs.  
 Read the credential with process.env.LTA\_ACCOUNT\_KEY and send it as the HTTP header  
 named exactly AccountKey. BEFORE the fetch, if that variable is missing or empty, return  
 503 with {"error":"LTA\_ACCOUNT\_KEY is not set. Add it in Vercel and redeploy."} and do not  
 call LTA at all; never let an unset variable reach the header, because JavaScript sends the  
 word "undefined" and LTA answers 401 exactly as it would for a wrong key.  
 AFTER the fetch, check response.ok before reading the body. LTA returns an empty body on  
 401, so calling response.json() on a failed reply throws and crashes the function. On a  
 non-2xx reply, return the upstream status and a one-line reason in your own JSON instead.  
 Set Cache-Control: s-maxage=20, stale-while-revalidate=40 on the bus response, because LTA  
 refreshes every 20 seconds. Treat an empty Services array as "no buses running", not as an  
 error. Note also that LTA returns NextBus2 and NextBus3 as objects whose fields are all  
 empty strings when there is no such bus: treat an empty EstimatedArrival as no bus and omit  
 it from the list, rather than computing a time from it. Never emit NaN or null as a minute. In the footer, add this exact line, which is what the licence asks for, with  
 the licence address as a working link:  
 "Contains information from LTA DataMall Bus Arrival accessed on \[DATE\] from the Land  
 Transport Authority (LTA DataMall), which is made available under the terms of the  
 Singapore Open Data Licence version 1.0 https\://data.gov.sg/open-data-licence. This is  
 an SMU course project and is not affiliated with or endorsed by the Land Transport  
 Authority." Leave my existing screens working.

GUARDRAILS: Never write the key into any file, any comment, or the README. Never create a  
 variable whose name starts with VITE\_. Never call datamall2.mytransport.sg from browser  
 code; every LTA call happens inside api/. Never print the key, or any part of it, in a  
 response or a log. No new npm packages. No database, no login. Do not use LTA's name or  
 logo in a way that suggests this app is official or endorsed.

CONTEXT: Deployed on Vercel from GitHub. The key lives only in environment variables  
 named LTA\_ACCOUNT\_KEY: AI Studio's Secrets for the preview, Vercel for the live site.  
 A real response from the endpoint looks like this:  
\[PASTE 15–25 LINES OF THE REAL RESPONSE HERE\]

