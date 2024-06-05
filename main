#include <iostream>
#include <vector>
#include <unordered_map>
#include <chrono>
#include <numeric>
#include <functional>

class Server {
public:
    std::string ip;
    int weight;
    int connections;
    std::chrono::milliseconds responseTime;

    Server(std::string ip, int weight = 1) : ip(ip), weight(weight), connections(0), responseTime(0) {}
};

// Round Robin
class RoundRobin {
private:
    std::vector<Server> servers;
    int currentIndex;

public:
    RoundRobin(std::vector<Server> servers) : servers(servers), currentIndex(0) {}

    Server& getNextServer() {
        Server& server = servers[currentIndex];
        currentIndex = (currentIndex + 1) % servers.size();
        return server;
    }
};

// Weighted Round Robin
class WeightedRoundRobin {
private:
    std::vector<Server> servers;
    int currentIndex;
    int currentWeight;

public:
    WeightedRoundRobin(std::vector<Server> servers) : servers(servers), currentIndex(0), currentWeight(0) {}

    Server& getNextServer() {
        while (true) {
            currentIndex = (currentIndex + 1) % servers.size();
            if (currentIndex == 0) {
                currentWeight -= std::gcd(servers[0].weight, servers[1].weight); // assuming all weights have the same GCD
                if (currentWeight <= 0) {
                    currentWeight = std::max_element(servers.begin(), servers.end(), 
                        [](const Server& a, const Server& b) { return a.weight < b.weight; })->weight;
                    if (currentWeight == 0) return servers[0];
                }
            }
            if (servers[currentIndex].weight >= currentWeight) {
                return servers[currentIndex];
            }
        }
    }
};

// Least Connections
class LeastConnections {
private:
    std::vector<Server> servers;

public:
    LeastConnections(std::vector<Server> servers) : servers(servers) {}

    Server& getNextServer() {
        return *std::min_element(servers.begin(), servers.end(), 
            [](const Server& a, const Server& b) { return a.connections < b.connections; });
    }
};

// Least Response Time
class LeastResponseTime {
private:
    std::vector<Server> servers;

public:
    LeastResponseTime(std::vector<Server> servers) : servers(servers) {}

    Server& getNextServer() {
        return *std::min_element(servers.begin(), servers.end(), 
            [](const Server& a, const Server& b) { return a.responseTime < b.responseTime; });
    }
};

// IP Hash
class IPHash {
private:
    std::vector<Server> servers;

public:
    IPHash(std::vector<Server> servers) : servers(servers) {}

    Server& getNextServer(const std::string& clientIP) {
        size_t hash = std::hash<std::string>{}(clientIP);
        return servers[hash % servers.size()];
    }
};

int main() {
    std::vector<Server> servers = { 
        Server("192.168.1.1", 1), 
        Server("192.168.1.2", 2), 
        Server("192.168.1.3", 3)
    };

    RoundRobin rr(servers);
    WeightedRoundRobin wrr(servers);
    LeastConnections lc(servers);
    LeastResponseTime lrt(servers);
    IPHash iphash(servers);

    // Simulate some requests
    std::vector<std::string> clientIPs = {"10.0.0.1", "10.0.0.2", "10.0.0.3"};

    std::cout << "Round Robin:" << std::endl;
    for (int i = 0; i < 6; ++i) {
        std::cout << rr.getNextServer().ip << std::endl;
    }

    std::cout << "Weighted Round Robin:" << std::endl;
    for (int i = 0; i < 6; ++i) {
        std::cout << wrr.getNextServer().ip << std::endl;
    }

    std::cout << "Least Connections:" << std::endl;
    servers[0].connections = 5;
    servers[1].connections = 2;
    servers[2].connections = 3;
    std::cout << lc.getNextServer().ip << std::endl;

    std::cout << "Least Response Time:" << std::endl;
    servers[0].responseTime = std::chrono::milliseconds(50);
    servers[1].responseTime = std::chrono::milliseconds(30);
    servers[2].responseTime = std::chrono::milliseconds(40);
    std::cout << lrt.getNextServer().ip << std::endl;

    std::cout << "IP Hash:" << std::endl;
    for (const auto& clientIP : clientIPs) {
        std::cout << iphash.getNextServer(clientIP).ip << std::endl;
    }

    return 0;
}
