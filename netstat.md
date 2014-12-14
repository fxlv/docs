# Netstat usage examples

## Listing sockets in LISTENING state

Use -l to list all LISTENING sockets

More specifically you can list all UDP or TCP or Unix sockets separately.

List all LISTENING UDP sockets
`netstat -nul`

List all LISTENING TCP sockets
`netstat -lt`

List all LISTENING unix sockets
`netstat -lx`
