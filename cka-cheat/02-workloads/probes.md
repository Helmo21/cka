Common failure scenarios (CKA) — Probes

# Case 1: CrashLoopBackOff caused by livenessProbe (early execution)

Symptoms

    Container restarts repeatedly

    Pod enters CrashLoopBackOff

    Logs show the application is still starting normally

Cause

    livenessProbe runs before the application has finished initializing

    No protection for slow startup

Fix

    Add a startupProbe to delay liveness checks

    Alternatively (less ideal), increase initialDelaySeconds

CKA trap

    Increasing initialDelaySeconds alone can hide real deadlocks later

    startupProbe is the correct long-term fix



# Case 2: Pod Running but Service unreachable

Symptoms

    Pod status is Running

    kubectl get endpoints does not include the Pod

    Service receives no traffic

    Direct access to Pod IP works

Cause

    readinessProbe is failing

Fix

    Inspect the readinessProbe configuration

    Validate endpoint, port, path, and timing

    Check application dependencies (DB, cache, config)

CKA trap

    Do not expect restarts

    Readiness failures are non-destructive by design



# Case 3: Rolling update stuck

Symptoms

    Deployment rollout never completes

    Old Pods remain running

    New Pods stay NotReady

Cause

    readinessProbe never succeeds on new Pods

    Deployment waits to keep minimum availability

Fix

    Fix readiness logic or endpoint

    Ensure application exposes readiness signal

    Verify maxUnavailable / maxSurge settings if relevant

CKA trap

    Liveness is not the problem here

    Rollouts are gated by readiness only



# Case 4: Traffic sent too early to application

Symptoms

    Application receives traffic immediately after Pod start

    Requests fail or return errors

    No probe failures reported

Cause

    No readinessProbe defined

    Pod is considered Ready by default

Fix

    Add a readinessProbe

    Delay traffic until the app is actually ready

CKA trap

    Many assume liveness controls traffic → false

    Readiness is the only traffic gate



# Case 5: Container restarts even though application is healthy

Symptoms

    Container restarts periodically

    Application logs show normal behavior

    Liveness probe fails intermittently

Cause

    Liveness probe timeout too strict

    Probe endpoint occasionally slow

Fix

    Increase timeoutSeconds

    Adjust failureThreshold

    Ensure probe endpoint is lightweight

CKA trap

    Liveness should detect deadlocks, not load spikes



# Case 6: StartupProbe causes unexpected restarts

Symptoms

    Container restarts during startup

    No liveness or readiness logs yet

Cause

    startupProbe.failureThreshold too low

    Application startup takes longer than allowed

Fix

    Increase failureThreshold

    Increase periodSeconds

    Match probe window to real startup time

CKA trap

    startupProbe can restart containers

    It is not “safe by default”



# Case 7: Application never receives traffic after recovery

Symptoms

    Application recovers internally

    Service still sends no traffic

    Pod stays Running

Cause

    readinessProbe keeps failing

    Application recovered but probe endpoint did not

Fix

    Fix readiness endpoint logic

    Ensure readiness reflects current state, not startup-only

CKA trap

    Kubernetes trusts the probe, not the logs



# Case 8: Misusing livenessProbe for dependency checks

Symptoms

    Container restarts when database/cache is down

    Application could otherwise recover gracefully

Cause

    livenessProbe checks external dependency

    Temporary dependency failure triggers restart

Fix

    Move dependency checks to readinessProbe

    Keep liveness focused on internal health only

CKA trap

    External dependencies should never control restarts