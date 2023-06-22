## using pprof
### install pprof
```console
go get -u github.com/google/pprof
```
### get profiling data
go support profiling many information:
+ **Goroutine**: stack traces of all current Goroutines
+ **CPU**: stack traces of CPU returned by runtime
+ **Heap**: a sampling of memory allocations of live objects
+ **Allocation**: a sampling of all past memory allocations
+ **Thread**: stack traces that led to the creation of new OS threads
+ **Block**: stack traces that led to blocking on synchronization primitives
+ **Mutex**: stack traces of holders of contended mutexes

We have three options to collect profile data:

#### 1. Using go test and benchmark
```console
go test -cpuprofile cpu.prof -memprofile mem.prof -bench .
```
#### 2. downloading from http

Import package http/net/pprof to inject pprof's enpoint getting profiling data
```go
import _ "net/http/pprof"
```
It actually initialize:
```go
func init() {
	http.HandleFunc("/debug/pprof/", Index)
	http.HandleFunc("/debug/pprof/cmdline", Cmdline)
	http.HandleFunc("/debug/pprof/profile", Profile)
	http.HandleFunc("/debug/pprof/symbol", Symbol)
	http.HandleFunc("/debug/pprof/trace", Trace)
}
```
Download profiling data
```console
curl -sK -v http://localhost:8082/debug/pprof/profile > profile.out
```

#### 3. Using pprof library in your code

Import package
```console
import github.com/pkg/profile
```
And then insert those lines in main function
```go
func main() {
	defer profile.Start(profile.ProfilePath(".")).Stop()
	// do something
}
```
### pprof tool provides many ways to visualize and analyse profile data
For example
+ interactive mode
```console
$ cd ~/pprof
$ pprof cpu.prof
```
type option **top** or **top10** to see detail
+ export SGV file format
```console
pprof -web pprof.samples.cpu.009.pb.gz
```
+ pprof web interface
```console
pprof -http :8080 pprof.samples.cpu.pb.gz
```
```console
pprof -edgefraction 0 -nodefraction 0 -nodecount 100000 -call_tree -http :8080 pprof.samples.cpu.pb.gz
```
using **-edgefraction** and **-nodefaction** to set limited nodes and edges of profile graph helps you to see recursive funtion call deeper.

**-call_tree** seperate trees base on the context.

Result
![Screen Shot 2021-09-30 at 14 47 23](https://user-images.githubusercontent.com/75965764/135410120-4530255a-c3c3-4e0f-9905-1544fc563fb8.png)
Flame Graph
![Screen Shot 2021-09-30 at 14 50 59](https://user-images.githubusercontent.com/75965764/135410609-3dd08b02-4b6a-43b6-96f9-241573a35934.png)

## using hey
this tool help to create simulator requests your API
### install
On Mac
```console
brew install hey
```
### Use
```console
hey -n 1000 -c 100 -m POST -d '{"trackity_id": "30F46FF1-3187-4832-A4C0-260CDBA77720", "page_code":"home_page", "platform": 4}' http://localhost:8080/v4/enrolls
```
<img width="1239" alt="hey" src="https://user-images.githubusercontent.com/75965764/135412244-1478da08-6a10-4631-83fc-e4498ca467f8.png">
