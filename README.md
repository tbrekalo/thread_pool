# threadpool

A c++ thread pool implementation inspired by https://github.com/progschj/ThreadPool.

## Dependencies

### Linux

Application uses following software:

1. gcc 4.8+

## Usage

If you would like to add threadpool to your project, include the thread_pool.hpp file while compiling, add -std=c++11 to your compiler flag list and -pthread to your linker list. For details on how to use the thread pool, please look at the example bellow:

    // define some functions you want to execute in parallel
    int function1(std::vector<int>& data, int index, ...) {
        ...
    }
    int function2(float a, float b) {
        ...
    }
    void function3(void) {
        ...
    }

    // create thread pool
    std::shared_ptr<THREAD_POOL::ThreadPool> thread_pool = THREAD_POOL::createThreadPool(); // or pass number of threads you desire
    // or std::unique_ptr<THREAD_POOL::ThreadPool> ...

    // create storage for return values of function1 and function2
    std::vector<std::future<int>> thread_futures;
    for (int i = 0; i < num_tasks; ++i) {
        thread_futures.emplace_back(thread_pool->submit_task(function1, std::ref(data), index, ...)); // be sure to use std::ref() when passing references!
        thread_futures.emplace_back(thread_pool->submit_task(function2, a, b));
    }

    // wait for threads to finish
    for (auto& it: thread_futures) {
        it.wait();
        // get return value with it.get();
    }

    // new set of tasks running function3
    std::vector<std::future<void>> thread_futures2;
    for (int i = 0; i < num_tasks2; ++i) {
        thread_futures2.emplace_back(thread_pool->submit_task(function3));
    }
    for (auto& it2: thread_futures2) {
        it.wait();
    }
