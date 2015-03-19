&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;For all of those excited about Apple's new programming language, I wanted to highlight an answer to a question I usually receive when talking about Swift with other iOS developers: "How do you make a "GET" API Request using Swift?". 

As you all know Apple's new programming language is immense and powerful. It's also extremely easy to understand, and will surely be the preferred language of choice for all iOS developers around the world. The only problem at the moment is that due to the languages infancy and lack of adequate examples, it can be a bit challenging when trying to develop full featured applications; especially if you're a developer whose become accustomed to the oracle that is StackOverflow.
For that very reason I have decided write a detailed blog post showing two examples of "GET" API Requests using the Swift programming language.

The goal of the code written below is to make a "GET" API Request to the iTunes server asking for all information related to "software" products containing the "keyword" specified as an argument.

In order to stay clear of any debates between NSURLConnection and NSURLSession, I have decided to provide adequate examples of both:


###  NSURLConnection

```swift,linenums=true
func searchItunesUsingNSURLConnectionFor(keyword: String) {
        
        let itunesSearchTerm = keyword.stringByReplacingOccurrencesOfString(" ", withString: "+", options: NSStringCompareOptions.CaseInsensitiveSearch, range: nil)
        if let escapedSearchTerm = itunesSearchTerm.stringByAddingPercentEscapesUsingEncoding(NSUTF8StringEncoding) {
            let urlPath = "http://itunes.apple.com/search?term=\(escapedSearchTerm)&media=software"
            var request : NSMutableURLRequest = NSMutableURLRequest()
            request.URL = NSURL(string: urlPath)
            request.HTTPMethod = "GET"
        
            NSURLConnection.sendAsynchronousRequest(request, queue: NSOperationQueue.mainQueue(), completionHandler:{ (response:NSURLResponse!, data: NSData!, error: NSError!) -> Void in
                println("Task completed")
                
                if(error != nil) {
                    println(error.localizedDescription)
                }
                
                var err: NSError?
                
                var jsonResult = NSJSONSerialization.JSONObjectWithData(data, options: NSJSONReadingOptions.AllowFragments, error: &err) as NSDictionary
                
                if(err != nil) {
                    println("JSON Error \(err!.localizedDescription)")
                }
                
                let results: NSArray = jsonResult["results"] as NSArray
                
                var arrays = []
                for result in results{
                    let appData = AppInfo (jsonData: result as NSDictionary)
                    self.sampleArray.append(appData)
                    println(appData.trackName)
                }
            })
        }
    }
```

###  NSURLSession

```swift,linenums=true
func searchItunesUsingNSURLSession(keyword: String) {
        
        let itunesSearchTerm = keyword.stringByReplacingOccurrencesOfString(" ", withString: "+", options: NSStringCompareOptions.CaseInsensitiveSearch, range: nil)
        if let escapedSearchTerm = itunesSearchTerm.stringByAddingPercentEscapesUsingEncoding(NSUTF8StringEncoding) {
                let urlPath = "http://itunes.apple.com/search?term=\(escapedSearchTerm)&media=software"
                var request : NSMutableURLRequest = NSMutableURLRequest()
                request.URL = NSURL(string: urlPath)
                request.HTTPMethod = "GET"
                
                let session = NSURLSession.sharedSession()
                let task = session.dataTaskWithRequest(request, completionHandler: {data, response, error -> Void in
                    println("Task completed")
                    if(error != nil) {
                        println(error.localizedDescription)
                    }
                    var err: NSError?
                    
                    var jsonResult = NSJSONSerialization.JSONObjectWithData(data, options: NSJSONReadingOptions.AllowFragments, error: &err) as NSDictionary
                    
                    if(err != nil) {
                        println("JSON Error \(err!.localizedDescription)")
                    }
                    
                    let results: NSArray = jsonResult["results"] as NSArray
                    
                    var arrays = []
                        for result in results{
                            let appData = AppInfo (jsonData: result as NSDictionary)
                            self.sampleArray.append(appData)
                            println(appData.trackName)
                        }
                })
            task.resume()
            }
        
        }
```

If the above methods are called correctly you should receive a success from the iTunes server with an array of "software" products related to the keyword you specified as an argument. If you did not, I would recommend that you revisit the examples above and verify that everything is written correctly.

As an open-source developer I welcome all contributions to the above post, so if you have any questions about the information written above, or would like to make a recommendation on how to improve it, please don't hesitate to contact me.

I appreciate you taking the time to read this post, so please let me know if I can be of any assistance with Objective-C, Swift, or any other iOS related topics and  questions in the future.

Thanks again,

Adam